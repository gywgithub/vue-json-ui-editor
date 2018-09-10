<template>
<el-row>
  <el-col :span="12">
    <el-input v-model="input.name"></el-input>
    <el-card class="form">
      <json-editor ref="JsonEditor" :schema="schema" v-model="model" v-if="show">
        <el-button type="primary" @click="submit" style="display:none;">Subscribe</el-button>
        <el-button type="reset" @click="reset">Reset</el-button>
      </json-editor>
      <el-button type="primary" @click="submit">Subscribe</el-button>
    </el-card>
  </el-col>
  <el-col :span="12">
    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <span>Model</span>
      </div>
      <pre class="json">{{ jsonString }}</pre>
    </el-card>
    <br />
    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <span>How to use</span>
      </div>
      <div class="json">
        <p>GitHub: <a href="https://github.com/yourtion/vue-json-ui-editor" target="_blank">vue-json-ui-editor</a></p>
        <p>NPM: <a href="https://www.npmjs.com/package/vue-json-ui-editor" target="_blank">json-editor</a></p>
      </div>
    </el-card>
  </el-col>
</el-row>
</template>

<script>
import JsonEditor from '../../src/JsonEditor.vue';
import { Option } from 'element-ui';
// let schema = require('@/schema/1');
import schema from '@/schema/1' 
import { setTimeout } from 'timers';
// console.log(1)
// let schema = require('./schema/1.json')
// console.log(schema)

export default {
  data: () => ({
    // schema: require('@/schema/1'),
    schema: null,
    // schema: require('@/schema/newsletter.json'),
    model: {},
    m1: [{ value: 'daily', label: 'Daily New' }, { value: 'promotion', label: 'Promotion' }],
    show: false,
    input: {
      name: ''
    },
    oldValue: ''
  }),
  watch: {
    model: {
      handler: function (val) {
        // console.log('val')
        console.log(val)
        // console.log(v2)
        // console.log('m')
        // console.log(this.schema)
        console.log('model 1')
        console.log(this.model)
        if (this.model.mode !== '' && this.model.mode !== sessionStorage.getItem('model')) {
          console.log('2222')
          sessionStorage.setItem('model', this.model.mode)
          this.$emit('changeSchema')
          // console.log('001')  
          // this.schema.properties.m1.anyOf = this.m1
          // console.log('new schema')
          // console.log(this.schema)
          // let newSchema = JSON.stringify(this.schema)
          // this.mode = {}
          // this.show = false
          // // this.schema = null
          // console.log('----------------')
          // console.log(this.schema)
          // console.log(newSchema)
          // console.log('3')
          
          // console.log('4')
          
          // setTimeout(() => {

            // this.schema = JSON.parse(newSchema)
            // this.show = true
            // this.init();
            // console.log('1')
            // this.schema.properties.m1.anyOf = this.m1
            // console.log('new schema')
            // console.log(this.schema)
            // this.schema = JSON.parse(newSchema)
            // console.log('schema')
            // console.log(this.schema)
            // this.show = true
            // this.show = true
            // console.log('49499')
            // this.init();
            // console.log('33333')
          // }, 2000)
        } else {
          console.log('002')
        }
      },
      deep: true
    },
    input: {
      handler: function (newVal, oldVal) {
        console.log('input')
        console.log(newVal)
        console.log(oldVal)
      },
      deep: true
    }
  },
  computed: {
    jsonString() {
      return JSON.stringify(this.model, null, 2).trim();
    },
  },
  methods: {
    submit(_e) {
      this.$refs.JsonEditor.form().validate(valid => {
        // console.log('valid');
        // console.log(valid);
        if (valid) {
          // this.model contains the valid data according your JSON Schema.
          // You can submit your model to the server here

          // eslint-disable-next-line no-console
          // console.log('model', JSON.stringify(this.model));
          this.$refs.JsonEditor.clearErrorMessage();
        } else {
          this.$refs.JsonEditor.setErrorMessage('Please fill out the required fields');
          return false;
        }
      });
    },
    reset() {
      this.$refs.JsonEditor.reset();
    },
    getData() {
      setTimeout(() => {
        console.log('1');
      }, 2000);
    },
    init() {
      JsonEditor.setComponent('form', 'el-form', ({ vm }) => {
        // vm is the JsonEditor VM

        const labelWidth = '120px';
        const model = vm.data;
        const rules = {};
        // console.log('vm');
        // console.log(vm);
        let propertiesObject = vm.schema.properties;
        // console.log('propertiesObject');
        // console.log(propertiesObject);
        // console.log('2')
        function parseField(fields) {
          Object.keys(fields).forEach(key => {
            if (key.indexOf('$') === 0 && key !== '$sub') return;
            const field = fields[key];
            if (field.$sub) {
              return parseField(field);
            }
            if (!field.name) return;
            rules[field.name] = [];
            const type = field.schemaType === 'array' && field.type === 'radio' ? 'string' : field.schemaType;
            const required = field.required;
            const message = field.title;
            const trigger = ['radio', 'checkbox', 'select', 'number'].includes(field.type) ? 'change' : 'blur';
            rules[field.name].push({ type, required, message, trigger });
            if (field.minlength !== undefined || field.maxlength !== undefined) {
              const max = field.maxlength || 255;
              const min = field.minlength || 0;
              const msg = `Length must between ${min} and ${max}`;
              rules[field.name].push({ min, max, message: msg, trigger });
            }

            if (field.type === 'number') {
              let name = field.name;
              let value = propertiesObject[name];
              let val = vm.data[name];
              if (value.maximum === undefined && value.minimum === undefined) {
                return true;
              } else if (value.maximum !== undefined && value.minimum === undefined && val > value.maximum) {
                const max = value.maximum;
                const msg = `The maximum value must be less than ${max}`;
                rules[field.name].push({ max, message: msg, trigger });
              } else if (value.minimum !== undefined && value.maximum === undefined && val < value.minimum) {
                const min = value.minimum;
                const msg = `The minimum value must be greater than ${min}`;
                rules[field.name].push({ min, message: msg, trigger });
              } else if (
                (value.maximum !== undefined && val > value.maximum) ||
                (value.minimum !== undefined && val < value.minimum)
              ) {
                const max = value.maximum;
                const min = value.minimum;
                const msg = `The maximum or minimum must between ${min} and ${max}`;
                rules[field.name].push({ min, max, message: msg, trigger });
              }
            }
          });
        }

        parseField(vm.fields);
        // console.log('vm fields')
        // console.log(vm.fields)
        // console.log('return result')
        // console.log({ labelWidth, rules, model })
        // returning the form props
        return { labelWidth, rules, model };
      });

      // http://element.eleme.io/#/en-US/component/form#validation
      JsonEditor.setComponent('label', 'el-form-item', ({ field }) => ({
        prop: field.name,
      }));

      JsonEditor.setComponent('email', 'el-input');
      JsonEditor.setComponent('url', 'el-input');
      JsonEditor.setComponent('number', 'el-input-number');
      JsonEditor.setComponent('text', 'el-input');
      JsonEditor.setComponent('textarea', 'el-input');
      JsonEditor.setComponent('checkbox', 'el-checkbox');
      JsonEditor.setComponent('checkboxgroup', 'el-checkbox-group');
      JsonEditor.setComponent('radio', 'el-radio');
      JsonEditor.setComponent('select', 'el-select');
      JsonEditor.setComponent('switch', 'el-switch');
      JsonEditor.setComponent('color', 'el-color-picker');
      JsonEditor.setComponent('rate', 'el-rate');
      JsonEditor.setComponent('tag', 'el-tag');

      // You can also use the component object
      JsonEditor.setComponent('option', Option);

      // By default `<h1/>` is used to render the form title.
      // To override this, use the `title` type:
      JsonEditor.setComponent('title', 'h2');

      // By default `<p/>` is used to render the form title.
      // To override this, use the `description` type:
      JsonEditor.setComponent('description', 'small');

      // By default `<div/>` is used to render the message error.
      // To override this, use the `error` type:
      JsonEditor.setComponent('error', 'el-alert', ({ vm }) => ({
        type: 'error',
        title: vm.error,
      }));
    },
  },
  mounted() {
    console.log('mounted');
    // console.log();
    // console.log(schema)
    
    // this.schema = schema;
    this.schema = JSON.parse(sessionStorage.getItem('schema'))
    console.log('s1')
    console.log(this.schema);
    console.log('s2')
    this.show = true
    // this.reset();
    if (sessionStorage.getItem('model')) {
      console.log('s3')
      this.model.mode = sessionStorage.getItem('model')
    }
    console.log('s4')
    this.init();
  },
  destoryed () {
    sessionStorage.removeItem('schema')
  },
  components: { JsonEditor },
};
</script>

<style>
.form {
  text-align: left;
  width: 90%;
  margin: auto;
}

h2 {
  font-size: 1.7em;
  text-align: center;
  margin-top: 0;
  margin-bottom: 0.2em;
}

h2 + small {
  display: block;
  text-align: center;
  margin-bottom: 1.2em;
}

small {
  line-height: 20px;
  display: block;
}

.el-alert {
  margin-bottom: 15px;
}

.el-form .sub {
  margin-left: 10%;
}

.json {
  text-align: left;
}
.el-form-item__content {
  /*display: none;*/
}
</style>

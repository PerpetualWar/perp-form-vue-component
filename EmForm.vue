<template>
  <form :class="inline && inlineClasses" @keyup.escape="$emit('esc')">
    <!-- @submit.prevent="" -->
    <slot :v="$v" :sent="sent" :sentChange="sentChange" :form="form"></slot>
  </form>
</template>

<script>
import {
  required,
  requiredIf,
  requiredUnless,
  minLength,
  maxLength,
  minValue,
  maxValue,
  alpha,
  alphaNum,
  numeric,
  decimal,
  integer,
  email,
  ipAddress,
  between,
  url,
  helpers,
} from 'vuelidate/lib/validators';

export default {
  name: 'EmForm',
  props: {
    validationProps: {
      type: Array,
      required: true,
      validator: prop => prop.every(e => typeof e === 'object'),
    },
    inline: { type: Boolean, default: false },
  },
  components: {
    //
  },
  data() {
    return {
      sent: false,
      form: {},
    };
  },
  validations() {
    let schema = {
      form: {
        //
        // property: {
        //   required,
        //   url,
        //   minLength: minLength(3),
        // },
      },
    };

    //populate schema.form with properties
    this.validationProps.forEach(valObj => {
      const rules = valObj.rules;
      rules.forEach(rule => {
        if (typeof rule !== 'object') {
          schema.form[valObj.name] = {
            ...schema.form[valObj.name],
            [rule]: this[rule],
          };
        } else {
          schema.form[valObj.name] = {
            ...schema.form[valObj.name],
            [rule.name]: this[rule.name](...rule.params),
          };
        }
      });
    });
    return schema;
  },
  created() {
    this.validationProps.forEach(valObj => {    
      //set must be used since we are dynamically adding properties
      this.$set(this.form, valObj.name, valObj.initValue);
    });
  },
  computed: {
    inlineClasses() {
      return 'form form-inline';
    },
  },
  methods: {
    required,
    requiredIf,
    requiredUnless,
    minLength,
    maxLength,
    minValue,
    maxValue,
    alpha,
    alphaNum,
    numeric,
    decimal,
    integer,
    email,
    ipAddress,
    between,
    url,
    sentChange(bool) {
      this.sent = bool;
    },
  },
};
</script>

<style lang="scss" scoped>
.submit {
  display: flex;
  justify-content: space-around;
  margin: 0 -5px;
  > * {
    flex: 1;
    margin: 0 5px;
  }
}
.error-message {
  color: red;
  font-size: 13px;
  padding-top: 3px;
}
.has-error {
  .el-input__inner {
    border-color: red;
  }
}
</style>

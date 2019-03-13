<template>
  <div>
    <label v-if="label" :class="{ 'i-form-item-label-required': isRequired }">{{ label }}</label>
    <div>
      <slot></slot>
      <div v-if="validateState==='error'" class="-form-item-message">{{ validateMessage }}</div>
    </div>
  </div>
</template>
<script>
import AsyncValidator from 'async-validator';
import Emitter from "../../mixins/emitter.js";
export default {
  name: "iFormItem",
  mixins: [Emitter],
  inject: ['form'],
  props: {
    label: {
      type: String,
      default: ''
    },
    prop: {
      type: String
    }
  },
  data () {
    return {
      isRequired: false,
      validateState: '',
      validateMessage: ''
    }
  },
  computed: {
    fieldValue () {
      return this.form.model[this.prop]
    }
  },
  methods: {
    getRules () {
      let formRules = this.form.rules
      formRules = formRules ? formRules[this.prop] : []
      return [].concat(formRules || [])
    },
    setRules () {
      let rules = this.getRules()
      if (rules.length) {
        rules.every((rule) => {
          this.isRequired = rule.required
        })
      }
      this.$on('on-form-blur', this.onFileBlur)
      this.$on('on-form-change', this.onFileChange)
    },
    onFileBlur () {
      this.validate('blur')
    },
    onFileChange () {
      this.validate('change')
    },
    // 重置数据
    resetField () {
      this.validateState = ''
      this.validateMessage = ''
      this.form.model[this.prop] = this.initialValue
    },
    getFilteredRule (trigger) {
      const rules = this.getRules()
      return rules.filter(rule => !rule.trigger || rule.trigger.indexOf(trigger) !== -1)
    },
    /**
     * 校验数据
     * @param trigger 校验类型
     * @param callback 回调函数
     */
    validate (trigger, callback = function () {}) {
      let rules = this.getFilteredRule(trigger)
      if (!rules || rules.length === 0) return true

      // 设置状态为校验中
      this.validateState = 'validating'

      // 以下为 async-validator 库的调用方法
      let descriptor = {}
      descriptor[this.prop] = rules

      const validator = new AsyncValidator(descriptor)
      let model = {}

      model[this.prop] = this.fieldValue
      validator.validate(model, { firstFields: true}, errors => {
        this.validateState = !errors ? 'success' : 'error'
        this.validateMessage = errors ? errors[0].message : ''
        callback(this.validateMessage)
      })
    }
  },
  mounted () {
    if (this.prop) {
      this.dispatch('iForm', 'on-form-item-add', this)

      // 设置初始值，以便在重置时恢复默认值
      this.initialValue = this.fieldValue

      this.setRules()
    }
  },
  beforeDestroy () {
    this.dispatch('iForm', 'on-form-item-remove', this)
  }
};
</script>
<style>
  .i-form-item-label-required:before {
    content: '*';
    color: red;
  }
  .i-form-item-message {
    color: red;
  }
</style>

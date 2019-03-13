<template>
  <input type="text" :value="currentValue" @input="handleInput" @blur="hanleBlur" />
</template>
<script>
import Emitter from "../../mixins/emitter.js";

export default {
  name: "iInput",
  mixins: [Emitter],
  data () {
    return {
      currentValue: this.value
    }
  },
  methods: {
    handleInput (event) {
      const value = event.target.value
      this.currentValue = value
      this.$emit('input', value)
      this.dispatch('iFormItem', 'on-form-change', value)
    },
    hanleBlur (event) {
      this.dispatch('iFormItem', 'on-form-change', this.currentValue)
    }
  }
};
</script>


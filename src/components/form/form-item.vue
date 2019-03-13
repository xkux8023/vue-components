<template>
  <div>
    <label v-if="label">{{ label }}</label>
    <div>
      <slot></slot>
    </div>
  </div>
</template>
<script>
import Emitter from "../../mixins/emitter.js";
export default {
  name: "iFormItem",
  props: {
    label: {
      type: String,
      default: ""
    },
    prop: {
      type: String
    }
  }
};
</script>

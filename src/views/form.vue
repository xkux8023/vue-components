<template>
  <div>
    <h3>具有数据校验功能的表单组件——Form</h3>
    <iForm>
      <iFormItem></iFormItem>
    </iForm>
  </div>
</template>
<script>
import iForm from "../components/form/form.vue";
import iFormItem from "../components/form/form-item.vue";
// import iInput from '../components/input/input.vue';

export default {
  components: { iForm, iFormItem }
};
</script>

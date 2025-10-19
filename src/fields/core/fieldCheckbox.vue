<template>
  <span
    :id="getFieldID(schema)"
    class="checkbox"
    :value="value"
    :class="{ active: value }"
    :disabled="disabled"
    @click="onChange"
  >
    {{ schema.label }}
  </span>
</template>

<script>
import abstractField from '../abstractField.js'

export default {
  name: 'FieldCheckbox',
  mixins: [ abstractField ],
  methods: {
    onChange(){
      this.value = Boolean(this.value)
      this.value = !this.value
    }
  }
}
</script>

<style lang="scss">
.vue-form-generator .field-checkbox .checkbox{
  color: #585858;
  background-color: #fff;
  padding: 5px 8px;
  margin: 29px auto 0;
  border-radius: 5px;
  border: 1px solid #ccc;
  -webkit-touch-callout: none; /* iOS Safari */
  -webkit-user-select: none; /* Safari */
  -khtml-user-select: none; /* Konqueror HTML */
  -moz-user-select: none; /* Firefox */
  -ms-user-select: none; /* Internet Explorer/Edge */
  user-select: none; /* Non-prefixed version, currently
        supported by Chrome and Opera */
  cursor: pointer;
}
.vue-form-generator .field-checkbox label{
  display: none;
}
.vue-form-generator .field-checkbox .active{
  color: #fff;
  background-color: #337ab7;
  border-color: #2e6da4;
}
</style>
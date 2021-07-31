<template>
  <div class="px-4 py-5 sm:p-6" v-on="$listeners">
    <v-select
      class="z-10"
      v-bind="{
        multiple: multiple,
        options: options,
        value: value,
      }"
      @input="setValue"
    />
  </div>
</template>
<script>
export default {
  props: {
    options: {
      default: () => [],
      type: Array
    },
    value: {
      type: [String, Array],
      required: true
    },
    multiple: {
      type: Boolean,
      default: () => false
    }
  },
  methods: {
    setValue (event) {
      this.$emit('input', event)
    }
  }
}
</script>

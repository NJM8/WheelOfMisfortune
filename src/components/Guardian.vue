<template>
  <div>
    <p>Guardian #{{ gNum + 1 }}:</p>
    <input type="text" :value="value" @input="sendName" />
  </div>
</template>

<script>
export default {
  name: "Guardian",
  props: ["gNum", "value"],
  methods: {
    sendName(e) {
      this.$emit("input", e.target.value);
    },
  },
};
</script>

<style>
.wrapper {
  display: flex;
}
</style>
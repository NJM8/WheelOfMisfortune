<template>
  <div>
    <p class="guardianCard">Guardian #{{ gNum + 1 }}:</p>
    <input
      type="text"
      :value="value"
      @input="sendName"
      placeholder="Guardian Name"
      class="card"
    />
  </div>
</template>

<script>
export default {
  name: "GuardianInput",
  props: ["gNum", "value"],
  methods: {
    sendName(e) {
      this.$emit("input", e.target.value);
    },
  },
};
</script>

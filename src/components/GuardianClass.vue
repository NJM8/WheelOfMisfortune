<template>
  <div class="guardianContainer">
    <div v-for="(guardian, index) in guardians" :key="index">
      <div class="encounterContainer guardianCard">
        <span class="guardianName marginRight">{{ guardian.name }}:</span>
        <span class="guardianClass marginRight"
          >{{ guardian.affinity }}, {{ guardian.class }},
          {{ guardian.subclass }}</span
        >
        <button @click="reRollClass(index)" class="baseButton">
          Re Roll
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "GuardianClass",
  props: {
    guardians: {
      type: Array,
      default: () => [],
    },
  },
  methods: {
    reRollClass(index) {
      this.$emit("re-roll-class", index);
    },
  },
};
</script>

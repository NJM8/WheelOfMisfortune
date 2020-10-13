<template>
  <div class="encounterContainer">
    <div
      v-for="(encounter, index) in encounters"
      :key="index"
      class="guardianCard encounterMargin"
    >
      <div class="encounterContainer">
        <span class="encounterName">{{ encounter.name }}</span>
        <span class="">{{ encounter.teamMod }}</span>
      </div>
      <div
        v-for="(encounterRoll, guardianName) in encounter.rolls"
        :key="guardianName"
      >
        <div class="encounterInnerContainer">
          <span class="guardianName marginRight">{{ guardianName }}:</span>
          <p class="marginRight">{{ encounterRoll }}</p>
          <button
            @click="reRollGuardianEncounter(index, guardianName)"
            class="baseButton marginRight"
          >
            Re Roll
          </button>
          <button
            v-if="encounterRoll.includes('+Exotic')"
            @click="reRollGuardianExotic(index, guardianName, encounterRoll)"
            class="baseButton"
          >
            Re Roll Exotic
          </button>
        </div>
      </div>
      <button class="baseButton mainButton" @click="removeEncounter(index)">
        Remove Encounter
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: "EncounterContainer",
  props: {
    encounters: {
      type: Array,
      default: () => [],
    },
  },
  methods: {
    reRollGuardianEncounter(index, guardianName) {
      this.$emit("re-roll-guardian-encounter", index, guardianName);
    },
    reRollGuardianExotic(index, guardianName, encounterRoll) {
      this.$emit("re-roll-guardian-exotic", index, guardianName, encounterRoll);
    },
    removeEncounter(index) {
      this.$emit("remove-encounter", index);
    },
  },
};
</script>

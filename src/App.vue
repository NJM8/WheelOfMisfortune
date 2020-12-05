<template>
  <div id="app" class="appContainer">
    <img src="./assets/ProphecyBackground.jpeg" alt="" class="background" />
    <h1 class="card">Wheel of Misfortune</h1>
    <InfoModal />
    <div style="width: 200px">
      <button @click="chooseRaid" class="baseButton marginRight">
        Choose Raid
      </button>
      <button v-if="raid.name.length > 0" class="baseButton" @click="resetRaid">
        Reset Raid
      </button>
    </div>
    <h1 v-if="raid.name.length > 0" class="card raidName">{{ raid.name }}</h1>
    <br />
    <div v-if="raid.name.length > 0" class="guardianContainer">
      <div v-for="(guardian, index) in raid.guardians" :key="index">
        <GuardianInput v-model="raid.guardians[index].name" :g-num="index" />
      </div>
    </div>
    <br />
    <div v-if="showChooseSubClasses && raid.name.length > 0" class="mainButton">
      <button @click="genClasses" class="baseButton">
        Choose Class and SubClasses
      </button>
    </div>
    <br />
    <GuardianClass
      v-if="showEncounters"
      :guardians="raid.guardians"
      @re-roll-class="reRollClass"
    />
    <div v-if="showEncounters" class="mainButton">
      <input
        type="text"
        v-model="newEncounterName"
        placeholder="Encounter Name"
        class="card nameInput encounterNameInput"
      />
      <button @click="addEncounter" class="baseButton">
        <!-- :disabled="newEncounterName.length === 0" -->
        Add Encounter
      </button>
    </div>
    <p>{{ raid.encounters.map((e) => e.teamMod) }}</p>
    <EncounterContainer
      :encounters="raid.encounters"
      @re-roll-guardian-encounter="reRollGuardianEncounter"
      @re-roll-guardian-exotic="reRollGuardianExotic"
      @remove-encounter="removeEncounter"
    />
  </div>
</template>

<script>
import "@/assets/styles.css";
import {
  RAIDS,
  GUARDIAN_CLASSES,
  CLASS_AFFINITY,
  SUB_CLASSES,
  TEAM_MODS,
  ENCOUNTER_ROLLS,
  EXOTICS,
  BASE_RAID,
} from "@/helpers/options.js";
import cloneDeep from "lodash/cloneDeep";

import GuardianInput from "@/components/GuardianInput.vue";
import GuardianClass from "@/components/GuardianClass.vue";
import EncounterContainer from "@/components/EncounterContainer.vue";
import InfoModal from "@/components/InfoModal.vue";

export default {
  name: "App",
  components: { GuardianInput, GuardianClass, EncounterContainer, InfoModal },
  data() {
    return {
      showInfo: false,
      newEncounterName: "",
      raid: cloneDeep(BASE_RAID),
    };
  },
  computed: {
    showEncounters() {
      return this.raid.guardians.every((guardian) => guardian.class.length > 0);
    },
    showChooseSubClasses() {
      return this.raid.guardians.every((guardian) => guardian.name.length > 0);
    },
  },
  watch: {
    raid: {
      deep: true,
      handler() {
        localStorage.setItem("wheel-of-misfortune", JSON.stringify(this.raid));
      },
    },
  },
  created() {
    const savedRaid = localStorage.getItem("wheel-of-misfortune") || false;
    if (savedRaid) {
      this.raid = JSON.parse(savedRaid);
    }
  },
  methods: {
    genRandomOption(dataSet, previousSelections) {
      let newOption = dataSet[Math.floor(Math.random() * dataSet.length)];
      if (previousSelections) {
        while (previousSelections.includes(newOption)) {
          newOption = dataSet[Math.floor(Math.random() * dataSet.length)];
        }
      }
      return newOption;
    },
    chooseRaid() {
      this.raid.name = this.genRandomOption(RAIDS);
    },
    addEncounter() {
      const newEncounter = {
        name: this.newEncounterName,
        teamMod: "",
        rolls: {},
      };

      const existingTeamMods = this.raid.encounters
        .map((encounter) => encounter.teamMod)
        .filter((mod) => mod !== "");

      const usedAllTeamMods = existingTeamMods.length === 3;
      const shouldUseTeamMod =
        this.genRandomOption([1, 2, 3, 4, 5, 6, 7, 8, 9, 10]) === 1;

      newEncounter.teamMod =
        shouldUseTeamMod && !usedAllTeamMods
          ? this.genRandomOption(TEAM_MODS, existingTeamMods)
          : "";

      if (newEncounter.teamMod && newEncounter.teamMod.includes("+Player")) {
        newEncounter.teamMod = newEncounter.teamMod.concat(
          " - ",
          this.genRandomOption(
            this.raid.guardians.map((guardian) => guardian.name)
          )
        );
      }
      this.raid.guardians.forEach(
        (guardian) =>
          (newEncounter.rolls[guardian.name] = this.genEncounterOption(
            guardian.name
          ))
      );

      this.raid.encounters.push(newEncounter);
      this.newEncounterName = "";
    },
    genEncounterOption(guardianName) {
      const existingRolls = this.raid.encounters.map(
        (encounter) => encounter.rolls[guardianName]
      );
      let roll = this.genRandomOption(ENCOUNTER_ROLLS, existingRolls);
      if (roll.includes("+Exotic")) {
        roll = roll.concat(` - ${this.genRandomOption(EXOTICS)}`);
      }

      return roll;
    },
    reRollGuardianEncounter(encounterIndex, guardianName) {
      this.raid.encounters[encounterIndex].rolls[
        guardianName
      ] = this.genEncounterOption(guardianName);
    },
    reRollGuardianExotic(encounterIndex, guardianName, originalRoll) {
      const newRoll = originalRoll.split(" - ")[0];
      this.raid.encounters[encounterIndex].rolls[guardianName] = newRoll.concat(
        ` - ${this.genRandomOption(EXOTICS)}`
      );
    },
    removeEncounter(index) {
      this.raid.encounters.splice(index, 1);
    },
    genClasses() {
      this.raid.guardians.forEach((guardian) => {
        guardian.class = this.genRandomOption(GUARDIAN_CLASSES);
        guardian.affinity = this.genRandomOption(CLASS_AFFINITY);
        guardian.affinity === "Stasis"
          ? (guardian.subclass = "")
          : (guardian.subclass = this.genRandomOption(SUB_CLASSES));
      });
    },
    reRollClass(index) {
      const thisguardian = this.raid.guardians[index];
      thisguardian.class = this.genRandomOption(GUARDIAN_CLASSES);
      thisguardian.affinity = this.genRandomOption(CLASS_AFFINITY);
      thisguardian.affinity === "Stasis"
        ? (thisguardian.subclass = "")
        : (thisguardian.subclass = this.genRandomOption(SUB_CLASSES));
    },
    resetRaid() {
      this.raid = {};
      this.raid = cloneDeep(BASE_RAID);
    },
  },
};
</script>

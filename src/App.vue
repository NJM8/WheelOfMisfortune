<template>
  <div id="app" class="appContainer">
    <img src="./assets/ProphecyBackground.jpeg" alt="" class="background" />
    <h1 class="card">Wheel of Misfortune</h1>
    <button class="baseButton infoButton" @click="showInfo = !showInfo">
      Info
    </button>
    <div v-if="showInfo" class="infoPosition">
      <p>Welcome to Wheel of Misfortune</p>
      <p>
        Click Choose Raid to get started, fill out all guardian names, then you
        will be able to select classes and subClasses
      </p>
      <p>Finally click Add Encounter to generate encounter loadouts</p>
      <p>
        Most of the encounter options are easy to understand, if the option does
        not state anything about heavy, ie: Double Shotguns, they don't get to
        use heavy.
      </p>
      <p>
        I tried to make most of the options middle of the road, not too hard but
        still fun.
      </p>
      <p>
        There are some options that will make things a HUGE pain in the ass.
        These sometimes lead to the most fun, try to stick with it!
      </p>
      <p>
        There are some options with a +Exotic modifier, in this case your exotic
        weapon is chosen for you.
      </p>
      <p>
        Also there is a +Respin on one option, this is because this is a team
        wide affect with a re-roll for the individuals weapon loadout, again
        this will be done automatically.
      </p>
      <p>
        If you get two of the same options in one encounter, re roll for more
        variation. Also if a guardian gets the same roll twice in a row, re
        roll.
      </p>
      <p>
        If any encounter gets too stressful or is too hard with the random
        loadout, feel free to use the meta to get through things.
      </p>
    </div>
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
        <Guardian v-model="raid.guardians[index].name" :g-num="index" />
      </div>
    </div>
    <br />
    <div v-if="showChooseSubClasses && raid.name.length > 0" class="mainButton">
      <button @click="genClasses" class="baseButton">
        Choose Class and SubClasses
      </button>
    </div>
    <br />
    <div v-if="showEncounters" class="guardianContainer">
      <div v-for="(guardian, index) in raid.guardians" :key="index">
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
    <div v-if="showEncounters" class="mainButton">
      <input
        type="text"
        v-model="newEncounterName"
        placeholder="Encounter Name"
        class="card nameInput encounterNameInput"
      />
      <button
        @click="addEncounter"
        class="baseButton"
        :disabled="newEncounterName.length === 0"
      >
        Add Encounter
      </button>
    </div>
    <div class="encounterContainer">
      <div
        v-for="(encounter, index) in raid.encounters"
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
              @click="reRollEncounter(index, guardianName)"
              class="baseButton marginRight"
            >
              Re Roll
            </button>
            <button
              v-if="encounterRoll.includes('+Exotic')"
              @click="reRollEncounterExotic(index, guardianName, encounterRoll)"
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
  </div>
</template>

<script>
import Guardian from "@/components/Guardian.vue";

export default {
  name: "App",
  components: { Guardian },
  data() {
    return {
      showInfo: false,
      newEncounterName: "",
      raids: [
        "Eater of Worlds",
        "Scourge of The Past",
        "Leviathan",
        "Spire of Stars",
        "Garden of Salvation",
        "Crown of Sorrow",
        "Last Wish",
      ],
      guardianClasses: ["Warlock", "Titan", "Hunter"],
      classAffinity: ["Solar", "Void", "Arc"],
      subClasses: ["Top Tree", "Middle Tree", "Bottom Tree"],
      teamMods: [
        "No HUD, Entire Team",
        "No Comms +Player",
        "No Mods, Entire Team",
      ],
      encounterRolls: [
        "Triple Snipers",
        "Triple Grenade Launchers",
        "Triple Shotguns",
        "Triple Bows",
        "Double Side Arms",
        "Double Auto Rifles",
        "Double SMG's, GL",
        "Double Hand Cannons",
        "All Blues",
        "All Blues +Exotic",
        "All Greens",
        "All Greens +Exotic",
        "Only Void weapons",
        "Only Arc weapons",
        "Only Solar weapons",
        "Only Abilites",
        "DIM Random loadout",
        "Only exotic weapon, Guardian choice",
        "Only exotic weapon +Exotic",
        "Full Auto Only, tape/hold down fire button",
        "Rainbow: White, Blue, Purple",
        "Only Close Range Weapons",
        "Only Long Range Weapons",
      ],
      exotics: [
        "Sweet Business",
        "Rat King",
        "Sturm",
        "The Jade Rabbit",
        "Cerberus+1",
        "Ace of Spades",
        "The Last Word",
        "Outbreak Perfected",
        "Monte Carlo",
        "Traveler's Chosen",
        "Mida Multi-Tool",
        "The Huckleberry",
        "Wish-Ender",
        "The Chaperone",
        "Arbalest",
        "Bad Juju",
        "Bastion",
        "Vigilance Wing",
        "Crimson",
        "SUROS Regime",
        "Malfeasance",
        "Izanagi's Burden",
        "Thorn",
        "Lumina",
        "Witherhoard",
        "Coldheart",
        "Graviton Lance",
        "Hard Light",
        "Prometheus Lens",
        "Trinity Ghoul",
        "Jotunn",
        "Eriana's Vow",
        "Devil's Ruin",
        "Ruinous Effigy",
        "Fighting Lion",
        "Skyburner's Oath",
        "Merciless",
        "Telesto",
        "Wavesplitter",
        "LeMonarque",
        "Divinity",
        "Tommy's Matchbook",
        "Sunshot",
        "Riskrunner",
        "Borealis",
        "Polaris Lance",
        "Lord of Wolves",
        "Tarrabah",
        "Symmetry",
        "The Fourth Horseman",
        "The Prospector",
        "D.A.R.C.I.",
        "Worldline Zero",
        "One Thousand Voices",
        "The Queenbreaker",
        "Truth",
        "Leviathan's Breath",
        "Tractor Cannon",
        "The Wardcliff Coil",
        "Sleeper Simulant",
        "Two-Tailed Fox",
        "Thunderlord",
        "Deathbringer",
        "Heir Apparent",
        "Legend of Acrius",
        "The Colony",
        "Whisper of the Worm",
        "Black Talon",
        "Anarchy",
        "Xenophage",
      ],
      raid: {
        name: "",
        guardians: [
          {
            name: "",
            class: "",
            affinity: "",
            subclass: "",
          },
          {
            name: "",
            class: "",
            affinity: "",
            subclass: "",
          },
          {
            name: "",
            class: "",
            affinity: "",
            subclass: "",
          },
          {
            name: "",
            class: "",
            affinity: "",
            subclass: "",
          },
          {
            name: "",
            class: "",
            affinity: "",
            subclass: "",
          },
          {
            name: "",
            class: "",
            affinity: "",
            subclass: "",
          },
        ],
        encounters: [],
      },
    };
  },
  computed: {
    showEncounters() {
      return this.raid.guardians.every((guardian) =>
        Object.values(guardian).every((val) => val.length > 0)
      );
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
    genRandomOption(dataSet) {
      return dataSet[Math.floor(Math.random() * dataSet.length)];
    },
    chooseRaid() {
      this.raid.name = this.genRandomOption(this.raids);
    },
    addEncounter() {
      const newEncounter = {
        name: this.newEncounterName,
        teamMod: "",
        rolls: {},
      };

      newEncounter.teamMod =
        this.genRandomOption([...Array(20).keys()].map((i) => i + 1)) === 1
          ? this.genRandomOption(this.teamMods)
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
          (newEncounter.rolls[guardian.name] = this.genEncounterOption())
      );

      this.raid.encounters.push(newEncounter);
      this.newEncounterName = "";
    },
    genEncounterOption() {
      let roll = this.genRandomOption(this.encounterRolls);
      if (roll.includes("+Exotic")) {
        roll = roll.concat(` - ${this.genRandomOption(this.exotics)}`);
      }

      return roll;
    },
    reRollEncounter(encounterIndex, guardianName) {
      this.raid.encounters[encounterIndex].rolls[
        guardianName
      ] = this.genEncounterOption();
    },
    reRollEncounterExotic(encounterIndex, guardianName, originalRoll) {
      const newRoll = originalRoll.split(" - ")[0];
      this.raid.encounters[encounterIndex].rolls[guardianName] = newRoll.concat(
        ` - ${this.genRandomOption(this.exotics)}`
      );
    },
    removeEncounter(index) {
      this.raid.encounters.splice(index, 1);
    },
    genClasses() {
      this.raid.guardians.forEach((guardian) => {
        guardian.class = this.genRandomOption(this.guardianClasses);
        guardian.affinity = this.genRandomOption(this.classAffinity);
        guardian.subclass = this.genRandomOption(this.subClasses);
      });
    },
    reRollClass(index) {
      const thisguardian = this.raid.guardians[index];
      thisguardian.class = this.genRandomOption(this.guardianClasses);
      thisguardian.affinity = this.genRandomOption(this.classAffinity);
      thisguardian.subclass = this.genRandomOption(this.subClasses);
    },
    resetRaid() {
      this.raid = {
        name: "",
        guardians: [
          {
            name: "",
            class: "",
            affinity: "",
            subclass: "",
          },
          {
            name: "",
            class: "",
            affinity: "",
            subclass: "",
          },
          {
            name: "",
            class: "",
            affinity: "",
            subclass: "",
          },
          {
            name: "",
            class: "",
            affinity: "",
            subclass: "",
          },
          {
            name: "",
            class: "",
            affinity: "",
            subclass: "",
          },
          {
            name: "",
            class: "",
            affinity: "",
            subclass: "",
          },
        ],
        encounters: [],
      };
    },
  },
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 20px;
}
.background {
  /* Set rules to fill background */
  min-height: 100%;
  min-width: 1024px;

  /* Set up proportionate scaling */
  width: 100%;
  height: auto;

  /* Set up positioning */
  position: fixed;
  top: 0;
  left: 0;
  z-index: -10;
  will-change: scroll-position;
}
.card {
  padding: 4px 8px;
  border: 1px solid ghostwhite;
  border-radius: 4px;
  background-color: ghostwhite;
}
.guardianCard {
  color: ghostwhite;
  padding: 8px 16px;
  background-color: rgba(25, 125, 75, 0.4);
  box-shadow: 0 1px 5px 1px rgba(0, 0, 0, 0.15);
  letter-spacing: 0.75px;
}
.baseButton {
  cursor: pointer;
  padding: 4px 8px;
  border: 1px solid ghostwhite;
  font-weight: 600;
  border-radius: 4px;
  background-color: ghostwhite;
}
.raidName {
  padding: 8px 16px;
  font-style: italic;
  font-weight: 700;
}
.guardianName {
  font-size: 1.1rem;
  font-weight: 600;
  padding: 4px;
}
.guardianClass {
  padding-bottom: 8px;
}
.sideBar {
  display: flex;
  flex-direction: column;
  width: 50px;
}
.slide-enter {
  transform: translateX(400px);
  opacity: 0;
}
.slide-enter-active {
  transition: all 500ms ease;
}
.slide-leave-active {
  transition: all 500ms ease;
  transform: translateX(-400px);
  opacity: 0;
}
.guardianContainer {
  width: 100%;
  max-width: 1600px;
  display: flex;
  justify-content: space-between;
  flex-wrap: wrap;
}
.appContainer {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding-left: 16px;
  padding-right: 16px;
}
.encounterContainer {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.encounterInnerContainer {
  display: flex;
  justify-content: center;
  align-items: baseline;
  height: 30px;
}
.marginRight {
  margin-right: 10px;
}
.mainButton {
  margin-top: 24px;
  margin-bottom: 8px;
}
.infoButton {
  position: absolute;
  top: 30px;
  right: 30px;
}
.infoPosition {
  position: absolute;
  width: 1000px;
  top: 80px;
  right: 30px;
  border: 1px solid grey;
  border-radius: 5px;
  background-color: white;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.16);
  padding: 16px;
}
.nameInput {
  cursor: pointer;
}
.encounterNameInput {
  margin-right: 16px;
}
.encounterMargin {
  margin: 16px 0px;
}
.encounterName {
  font-size: 1.4rem;
  font-weight: 600;
  font-style: italic;
  margin-bottom: 6px;
}
</style>

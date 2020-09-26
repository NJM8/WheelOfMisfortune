<template>
  <div id="app" class="appContainer">
    <h1>Wheel of Misfortune</h1>
    <button class="infoButton" @click="showInfo = !showInfo">Info</button>
    <div v-if="showInfo" class="infoPosition">
      <p>Welcome to Wheel of Misfortune</p>
      <p>
        Click Choose Raid to get started, fill out all gaurdian names, then you
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
      <button @click="chooseRaid" class="marginRight">Choose Raid</button>
      <button v-if="raid.name.length > 0" @click="resetRaid">Reset Raid</button>
    </div>
    <h2>{{ raid.name }}</h2>
    <br />
    <div v-if="raid.name.length > 0" class="gaurdianContainer">
      <div v-for="(guardian, index) in raid.gaurdians" :key="index">
        <Guardian v-model="raid.gaurdians[index].name" :g-num="index" />
      </div>
    </div>
    <br />
    <div v-if="showChooseSubClasses && raid.name.length > 0" class="mainButton">
      <button @click="genClasses">Choose Class and SubClasses</button>
    </div>
    <div v-if="showEncounters" class="gaurdianContainer">
      <div v-for="(guardian, index) in raid.gaurdians" :key="index">
        <div class="encounterContainer">
          <h4 class="marginRight">{{ guardian.name }}:</h4>
          <span class="marginRight">{{ guardian.class }}</span>
          <span class="marginRight">{{ guardian.affinity }}</span>
          <span class="marginRight" style="padding-bottom: 8px">{{
            guardian.subclass
          }}</span>
          <button @click="reRollClass(index)">Re Roll</button>
        </div>
      </div>
    </div>
    <div v-if="showEncounters" class="mainButton">
      <button @click="addEncounter">Add Encounter</button>
    </div>
    <div class="encounterContainer">
      <div v-for="(encounter, index) in raid.encounters" :key="index">
        <div
          v-for="(encounterRoll, gaurdianName) in encounter"
          :key="gaurdianName"
        >
          <div class="encounterInnerContainer">
            <h4 class="marginRight">{{ gaurdianName }}:</h4>
            <p class="marginRight">{{ encounterRoll }}</p>
            <button @click="reRollEncounter(index, gaurdianName)">
              Re Roll
            </button>
          </div>
        </div>
        <button class="mainButton" @click="removeEncounter(index)">
          Remove Encounter
        </button>
        <p>-----------------------------</p>
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
      encounterRolls: [
        "Triple Snipers",
        "Triple Grenade Launchers",
        "Triple Shotguns",
        "All Blues",
        "All Blues +Exotic",
        "All Greens",
        "All Greens +Exotic",
        "No Exotics",
        "Only Abilites",
        "DIM Random loadout",
        "Double Side Arms",
        "Hand cannon, Side Arm, GL",
        "Sniper, Shotgun, Any",
        "Side arm, GL, Any",
        "Only Void weapons",
        "Only Arc weapons",
        "Only Solar weapons",
        "Only your exotic weapon",
        "Only your exotic weapon +Exotic",
        "Double Auto Rifles",
        "Shotgun, Auto Rifle, Linear Fusion",
        "Triple Bows",
        "Bow +Exotic",
        "Bow, Sniper, Any",
        "Double SMG's, GL",
        "Year 1 Weapons only",
        "SMG, +Exotic",
        "Full Auto Only, tape/hold down fire button",
        "Rainbow: White, Blue, Purple",
        "No comms",
        "No Hud (entire team) +Respin",
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
        gaurdians: [
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
          }
        ],
        encounters: []
      },
    };
  },
  computed: {
    showEncounters() {
      return this.raid.gaurdians.every((guardian) =>
        Object.values(guardian).every((val) => val.length > 0)
      );
    },
    showChooseSubClasses() {
      return this.raid.gaurdians.every((guardian) => guardian.name.length > 0);
    },
  },
  watch: {
    raid: {
      deep: true,
      handler() {
        localStorage.setItem('wheel-of-misfortune', JSON.stringify(this.raid))
      }
    }
  },
  created() {
    const savedRaid = localStorage.getItem('wheel-of-misfortune') || false
    if (savedRaid) {
      this.raid = JSON.parse(savedRaid)
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
      const newEncounter = {};
      this.raid.gaurdians.forEach(
        (guardian) => (newEncounter[guardian.name] = this.genEncounterOption())
      );

      this.raid.encounters.push(newEncounter);
    },
    genEncounterOption() {
      let roll = this.genRandomOption(this.encounterRolls);
      if (roll.includes("+Respin")) {
        roll = roll.concat(` - ${this.genRandomOption(this.encounterRolls)}`);
      }
      if (roll.includes("+Exotic")) {
        roll = roll.concat(` - ${this.genRandomOption(this.exotics)}`);
      }

      return roll;
    },
    reRollEncounter(encounterIndex, gaurdianName) {
      this.raid.encounters[encounterIndex][
        gaurdianName
      ] = this.genEncounterOption();
    },
    removeEncounter(index) {
      this.raid.encounters.splice(index, 1);
    },
    genClasses() {
      this.raid.gaurdians.forEach((guardian) => {
        guardian.class = this.genRandomOption(this.guardianClasses);
        guardian.affinity = this.genRandomOption(this.classAffinity);
        guardian.subclass = this.genRandomOption(this.subClasses);
      });
    },
    reRollClass(index) {
      const thisGaurdian = this.raid.gaurdians[index];
      thisGaurdian.class = this.genRandomOption(this.guardianClasses);
      thisGaurdian.affinity = this.genRandomOption(this.classAffinity);
      thisGaurdian.subclass = this.genRandomOption(this.subClasses);
    },
    resetRaid() {
      this.raid = {
        name: "",
        gaurdians: [
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
          }
        ],
        encounters: []
      }
    }
  }
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
.gaurdianContainer {
  width: 100%;
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
</style>

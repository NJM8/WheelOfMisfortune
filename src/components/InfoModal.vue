<template>
  <div>
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
  </div>
</template>

<script>
export default {
  name: "InfoModal",
  data() {
    return {
      showInfo: false,
    };
  },
};
</script>

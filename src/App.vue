<template>
  <main-screen
    v-if="statusMatch === 'default'"
    @onStart="onHandleBeforeStart($event)"
  />
  <interact-screen
    v-if="statusMatch === 'match'"
    :cardsContext="settings.cardsContext"
    @onFinish="onGetResult"
  ></interact-screen>
  <result-screen
    v-if="statusMatch === 'result'"
    :timer="timer"
    @StartAgain="onStartAgain"
  ></result-screen>
  <p class="copyright">
    This game owned by Dương in Vue 3 course for begginers -
    <a href="#">view here</a>
  </p>
</template>

<script>
import MainScreen from "./components/MainScreen.vue";
import ResultScreen from "./components/ResultScreen.vue";
import InteractScreen from "./components/InteractScreen.vue";
import shuffled from "./utils/array.js";
export default {
  name: "App",
  components: {
    MainScreen,
    ResultScreen,
    InteractScreen,
  },
  data() {
    return {
      settings: {
        totalBlocks: 0,
        cardsContext: [],
        startedAt: null,
      },
      statusMatch: "default",
      timer: "",
    };
  },
  methods: {
    onHandleBeforeStart(config) {
      this.settings.totalBlocks = config.totalBlocks;
      const firstsCards = Array.from(
        {
          length: this.settings.totalBlocks / 2,
        },
        (_, i) => i + 1
      );
      const secondsCards = [...firstsCards];
      const cards = [...firstsCards, ...secondsCards];
      this.settings.cardsContext = shuffled(
        shuffled(shuffled(shuffled(cards)))
      );
      this.settings.startedAt = new Date();
      this.statusMatch = "match";
    },
    onGetResult() {
      const time = Math.round((new Date() - this.settings.startedAt) / 100);
      let seconds = "00";
      if (time % 60 < 10) {
        seconds = `0${time % 60}`;
      } else if (time % 60 >= 10) {
        seconds = time % 60;
      }
      this.timer = `${Math.floor(time / 60)} minutes ${seconds} seconds`;
      this.statusMatch = "result";
    },
    onStartAgain() {
      this.statusMatch = "default";
      this.timer = "";
    },
  },
};
</script>

<style lang="css" scoped>
.copyright {
  position: fixed;
  left: 50%;
  transform: translateX(-50%);
  bottom: 1.5rem;
  color: var(--light);
  z-index: 3;
  font-size: 1.5rem;
}
.copyright a {
  color: #f4dc26;
}
</style>

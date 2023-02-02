<template>
  <div class="screen">
    <div
      class="screen_inner"
      :style="{
      width: `${
          ((((920 - 16 * 4) / Math.sqrt(cardsContext.length) - 16) * 3) / 4 +
            16) *
          Math.sqrt(cardsContext.length)
        }px`,
    }"
    >
      <card-flip
        v-for="(card, index) in cardsContext"
        :key="index"
        :ref="`card-${index}`"
        :imgBackFaceUrl="`images/${card}.png`"
        :card="{index, value: card}"
        :cardsContext="cardsContext"
        @onFlip="checkRule($event)"
      />
    </div>
  </div>
</template>

<script>
import CardFlip from "./CardFlip.vue";
export default {
  props: {
    cardsContext: {
      type: Array,
      default: () => [],
    },
  },
  components: {
    CardFlip,
  },
  data() {
    return {
      rules: [],
    };
  },
  methods: {
    checkRule(card) {
      this.rules.push(card);
      if (
        this.rules.length === 2 &&
        this.rules[0].value === this.rules[1].value &&
        this.rules[0].index !== this.rules[1].index
      ) {
        this.$refs[`card-${this.rules[0].index}`][0].onEnableDisableMode();
        this.$refs[`card-${this.rules[1].index}`][0].onEnableDisableMode();
        this.rules = [];
        const disableElements = document.querySelectorAll(
          ".screen .card.disabled"
        );
        if (disableElements?.length == this.cardsContext.length - 2) {
          setTimeout(() => {
            this.$emit("onFinish");
          }, 950);
        }
      } else if (
        this.rules.length === 2 &&
        this.rules[0].value !== this.rules[1].value
      ) {
        const index0 = this.rules[0].index;
        const index1 = this.rules[1].index;
        this.rules = [];
        setTimeout(() => {
          this.$refs[`card-${index0}`][0].onFlipBackCard();
          this.$refs[`card-${index1}`][0].onFlipBackCard();
        }, 900);
      } else if (
        this.rules.length === 2 &&
        this.rules[0].index === this.rules[1].index
      ) {
        const index = this.rules[0].index;
        this.rules = [];
        setTimeout(() => {
          this.$refs[`card-${index}`][0].onFlipBackCard();
        }, 900);
      } else return false;
    },
  },
};
</script>

<style lang="css" scoped>
.screen {
  width: 100%;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 2;
  background-color: var(--dark);
  color: var(--light);
}
.screen_inner {
  width: calc(424px);
  display: flex;
  flex-wrap: wrap;
  margin: 2rem auto;
}
</style>

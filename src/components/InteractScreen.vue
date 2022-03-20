<template>
  <div class="screen">
    <div
      class="screen__inner"
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
        :card="{ index, value: card }"
        :indexCard="index"
        :cardsContext="cardsContext"
        @onFlip="checkRule($event)"
        :rules="this.rules"
      />
    </div>
  </div>
</template>

<script>
import CardFlip from "./Card.vue";
export default {
  props: {
    cardsContext: {
      type: Array,
      default: function () {
        return [];
      },
    },
  },
  components: {
    CardFlip,
  },
  data() {
    return {
      rules: [],
      isFlipping: false,
    };
  },
  methods: {
    checkRule(card) {
      if (this.rules.length === 2) return false;

      this.rules.push(card);
      if (this.rules.length === 2) {
        if (this.rules[0].index != this.rules[1].index) {
          if (this.rules[0].value === this.rules[1].value) {
            console.log("Right...");
            // // add class 'disabled' to component card
            setTimeout(() => {
              //   // close two card
              this.$refs[
                `card-${this.rules[0].index}`
              ][0].onEnabledDisableMode();
              this.$refs[
                `card-${this.rules[1].index}`
              ][0].onEnabledDisableMode();

              // // reset rules to []
              this.rules = [];
            }, 800);
            const disabledElements = document.querySelectorAll(
              ".screen .card.disabled"
            );

            if (
              disabledElements &&
              disabledElements.length === this.cardsContext.length - 2
            ) {
              setTimeout(() => {
                this.$emit("onFinish");
              }, 920);
            }
          } else {
            console.log("Wrong...");
            setTimeout(() => {
              //   // close two card
              this.$refs[`card-${this.rules[0].index}`][0].onFlipBackCard();
              this.$refs[`card-${this.rules[1].index}`][0].onFlipBackCard();

              //   // reset rules to []
              this.rules = [];
            }, 800);
          }
        }else this.rules = [];
      }
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

.screen__inner {
  display: flex;
  flex-wrap: wrap;
  margin: 2rem auto;
}
</style>

<script>
import { reactive, toRefs, computed } from "vue";

export default {
  setup() {
    const state = reactive({
      gradients: [
        {
          color: "#513781",
          xPosition: "20",
          yPosition: "20",
        },
      ],
      css: computed(() => {
        let cssString;
        state.gradients.forEach((element) => {
          console.log(element);
          cssString = `radial-gradient(100% 100% at ${element.xPosition}% ${element.yPosition}%, ${element.color} 10%, transparent)`;
          console.log(cssString);
        });
        return cssString;
      }),
    });
    return {
      ...toRefs(state),
    };
  },
};
</script>

<template>
  <div class="bg-slate-900 h-screen">
    <div class="w-full h-96 bg-gray-700 text-white">
      <input
        type="color"
        @input="$emit('gradient-change', gradient)"
        v-model="gradients[0].color"
      />
      <h1>{{ gradients[0].color }}</h1>
      <input type="range" min="1" max="100" v-model="gradients[0].xPosition" />
      <input type="range" min="1" max="100" v-model="gradients[0].yPosition" />
    </div>
  </div>
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>

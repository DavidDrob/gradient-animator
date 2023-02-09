<script setup>
import { ref } from "vue";

const props = defineProps({
  cssString: String,
  properties: Array,
  animationCssString: String,
  animationsEnabled: Boolean,
});

const modalOpen = ref(false);
const withAnimations = ref(true);

function copyCSS() {
  let text = props.cssString;
  if (withAnimations.value) {
    props.properties.forEach((property) => {
      text += property;
    });
    text += props.animationCssString;
  }
  navigator.clipboard.writeText(text);
}
</script>

<template>
  <button
    class="
      font-bold
      px-5
      py-2
      rounded-md
      ml-10
      hover:shadow-md
      bg-cyan-400
      text-white
    "
    @click="modalOpen = true"
  >
    <p>Copy CSS</p>
  </button>

  <div
    class="
      fixed
      top-0
      left-0
      right-0
      z-50
      w-full
      p-4
      overflow-x-hidden overflow-y-auto
      bg-black/30
      md:inset-0 md:h-full
    "
    :class="[modalOpen ? 'grid place-items-center' : 'hidden']"
  >
    <div class="w-full h-auto max-w-4xl">
      <div class="bg-white rounded-md shadow">
        <p class="text-xl font-bold pt-4 text-gray-800">
          Copy the following CSS
        </p>
        <p class="text-gray-800">
          And add
          <code class="bg-gray-200 px-1">animation-name: main;</code>
          to an element you want to animate
        </p>
        <div class="flex items-baseline justify-between px-6 space-y-6">
          <code
            class="text-base text-left leading-relaxed text-gray-600 w-2/3 pr-4"
          >
            <p>
              {{ cssString }}
            </p>
            <div v-if="withAnimations">
              <br />
              <p v-for="property in properties" :key="property">
                {{ property }}
              </p>
              <br />
              <p>
                {{ animationCssString }}
              </p>
            </div>
          </code>
          <div>
            <div
              class="h-72 w-56 mb-2 rounded-md noselect"
              :style="cssString"
              :id="withAnimations ? 'gradient' : ''"
            >
              &nbsp;
            </div>
            <div v-if="animationsEnabled">
              <span> Animations: </span>
              <input
                class="h-4 w-4 border-none rounded-md my-1 cursor-pointer"
                type="checkbox"
                v-model="withAnimations"
              />
            </div>
          </div>
        </div>
        <div class="flex justify-between w-full p-6">
          <button
            class="
              font-bold
              px-5
              py-2
              rounded-md
              hover:shadow-md
              bg-cyan-400
              text-white
            "
            @click="copyCSS"
          >
            <p>Copy to clipboard</p>
          </button>
          <button
            class="
              font-bold
              px-5
              py-2
              rounded-md
              hover:shadow-md
              bg-cyan-400
              text-white
            "
            @click="modalOpen = false"
          >
            <p>I'm done</p>
          </button>
        </div>
      </div>
    </div>
  </div>
</template>


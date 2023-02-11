<script setup>
import { ref, computed } from "vue";

const props = defineProps({
  cssString: String,
  properties: Array,
  animationCssString: String,
  animationsEnabled: Boolean,
  animationDuration: String,
  animationEasing: String,
});

const emit = defineEmits(["update:animationsEnabled"]);

const modalOpen = ref(false);

function copyCSS() {
  let text = document.querySelector("#cssCode").textContent;
  navigator.clipboard.writeText(text);
}

const animationToggled = computed({
  get() {
    return props.animationsEnabled;
  },
  set(value) {
    emit("update:animationsEnabled", value);
  },
});
</script>

<template>
  <button
    class="
      font-bold
      px-5
      py-2
      rounded-xl
      ml-10
      hover:shadow-md
      bg-gradient-to-bl
      btn
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
      <div class="bg-white rounded-xl shadow">
        <p class="text-xl font-bold pt-4 text-gray-800">
          Copy the following CSS
        </p>
        <p class="text-lg font-light text-yellow-400/80">
          As of right now the animations don't work in Safari and Firefox
        </p>
        <div class="flex items-baseline justify-between px-6 space-y-6">
          <code
            class="text-base text-left leading-relaxed text-gray-600 w-2/3 pr-4"
            id="cssCode"
          >
            <div>
              .your-element { <br />
              <div class="pl-3">
                {{ cssString }} <br />
                <div v-if="animationToggled">
                  animation-name: main; <br />
                  animation-iteration-count: infinite; <br />
                  animation-duration: {{ animationDuration }}; <br />
                  transition-timing-function: {{ animationEasing }} <br />
                </div>
              </div>

              }
            </div>
            <p></p>
            <div v-if="animationToggled">
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
              class="h-72 w-56 mb-2 rounded-xl noselect"
              :style="cssString"
              :id="animationToggled ? 'gradient' : ''"
            >
              &nbsp;
            </div>
            <div>
              <span> Animations: </span>
              <input
                class="h-4 w-4 border-none rounded-md my-1 cursor-pointer"
                type="checkbox"
                v-model="animationToggled"
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
              rounded-lg
              hover:shadow-md
              btn
              bg-gradient-to-tr
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
              rounded-lg
              hover:shadow-md
              btn
              bg-gradient-to-bl
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


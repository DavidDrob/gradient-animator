<script setup>
import { ref, computed } from "vue";

const props = defineProps({
  cssString: String,
  properties: Array,
  rootVariables: Array,
  animationCssString: String,
  animationsEnabled: Boolean,
  animationDuration: String,
  animationEasing: String,
  animationName: String,
});

const emit = defineEmits(["update:animationsEnabled"]);

const modalOpen = ref(false);

function copyCSS() {
  let text = ".your-element {";

  if (props.animationsEnabled) {
    text += props.cssString;
    text += "animation-name: " + props.animationName + "; ";
    text += "animation-iteration-count: infinite; ";
    text += "animation-duration: " + props.animationDuration + "; ";
    text += "transition-timing-function: " + props.animationEasing + ";}";

    props.properties.forEach((property) => {
      text += property;
    });

    text += ":root {";
    props.rootVariables.forEach((variable) => {
      text += `${variable.key}: ${variable.value};`;
    });
    text += "}";

    text += props.animationCssString;
  } else {
    text = document.querySelector("#cssCode").textContent;
  }

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
      py-3
      rounded-xl
      hover:shadow-md
      bg-gradient-to-bl bg-cyan-400
      text-white
    "
    @click="modalOpen = true"
  >
    Copy CSS
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
      md:inset-0
      h-full
    "
    :class="[modalOpen ? 'grid place-items-center' : 'hidden']"
  >
    <div class="w-full h-auto max-w-4xl">
      <div class="bg-white rounded-xl shadow">
        <p class="text-lg md:text-xl font-bold pt-4 text-center text-gray-800">
          Copy the following CSS
        </p>
        <p class="text-md md:text-lg font-light text-center text-yellow-600">
          As of right now the animations feature doesn't work in Safari and
          Firefox
        </p>
        <div
          class="
            flex flex-col-reverse
            md:flex-row
            items-center
            md:items-baseline
            justify-between
            px-6
            space-y-6
          "
        >
          <code
            class="
              text-base text-left
              leading-relaxed
              text-gray-600
              w-10/12
              md:w-2/3
              pr-4
            "
            id="cssCode"
          >
            <div>
              .your-element { <br />
              <div class="pl-3">
                {{ cssString }} <br />
                <div v-if="animationToggled">
                  animation-name: {{ animationName ? animationName : "main" }};
                  <br />
                  animation-iteration-count: infinite; <br />
                  animation-duration: {{ animationDuration }}; <br />
                  transition-timing-function: {{ animationEasing }} <br />
                </div>
              </div>

              }
            </div>
            <div v-if="animationToggled">
              <br />
              <p v-for="property in properties" :key="property">
                {{ property }}
              </p>
              <br />
              <p>:root {</p>
              <p class="pl-3" v-for="variable in rootVariables" :key="variable">
                {{ variable.key }}: {{ variable.value }}
              </p>
              <p>}</p>
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
              :id="animationToggled ? animationName : ''"
            >
              &nbsp;
            </div>
            <div class="text-center" v-if="animationName === 'main'">
              <span> Animations </span>
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
              rounded-lg
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


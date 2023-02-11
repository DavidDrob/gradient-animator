<script setup>
import { ref, watch, onMounted, computed } from "vue";
import GradientCanvas from "./components/GradientCanvas.vue";
import GradientSettings from "./components/GradientSettings.vue";
import AnimationSettings from "./components/AnimationSettings.vue";
import CSSModal from "./components/CSSModal.vue";
import GradientLibrary from "./components/GradientLibrary.vue";

const bgColor = ref("#2f1d49");

const gradients = ref([
  {
    id: 0,
    color: "#3cc2dd",
    xPosition: 20,
    yPosition: 80,
    strength: 0,
    hidden: false,
    keypoints: [
      {
        xPosition: 100,
        yPosition: 20,
        time: 100,
      },
    ],
  },
  {
    id: 1,
    color: "#987aF1",
    xPosition: 80,
    yPosition: 20,
    strength: 0,
    hidden: false,
    keypoints: [
      {
        xPosition: 0,
        yPosition: 100,
        time: 48,
      },
    ],
  },
]);

const animationCSSString = ref("");
const properties = ref([]);
const animationTime = ref(2);
const animationTimeCss = computed(() => {
  return animationTime.value + "s";
});
const animationEasing = ref("ease-in-out");
const screenSize = ref("normal");

const animationsEnabledGlobally = ref(false);
const openKeypoint = ref(false);
const maxIdGlobal = ref(1);

watch(animationsEnabledGlobally, async (newValue, oldValue) => {
  // restart animation on toggle
  const el = document.getElementById("gradient");
  el.style.animation = "none";
  el.offsetHeight;
  el.style.animation = null;

  let ids = [];
  if (newValue) {
    gradients.value.forEach((gradient) => {
      ids.push(gradient.id);
    });
    ids.forEach((id) => {
      changeAnimations(id);
    });
  } else animationCSSString.value = "";
});

onMounted(() => {
  gradients.value.forEach((gradient) => {
    initCSSVariables(gradient.id, gradient.xPosition, gradient.yPosition);
  });
});

function removeGradient(index) {
  const gradient = gradients.value.findIndex(
    (gradient) => gradient.id == index
  );
  if (gradients.value.length) maxIdGlobal.value = index + 1; // to avoid same ID error
  gradients.value.splice(gradient, 1);
}

const cssString = () => {
  var baseString = "background: ";
  gradients.value.forEach((gradient) => {
    if (gradient.hidden) return;
    baseString += createCSS(
      gradient.id,
      gradient.xPosition,
      gradient.yPosition,
      gradient.color,
      gradient.strength,
      gradient.keypoints.length > 0 && animationsEnabledGlobally.value
    );
  });
  return baseString + bgColor.value + ";";
};

function createCSS(id, x, y, color, strength, animationsEnabled) {
  return `radial-gradient(100% 100% at ${
    animationsEnabled
      ? `var(--${id}-x-position) var(--${id}-y-position)`
      : `${parseInt(x)}% ${parseInt(y)}%`
  }, ${color} ${strength}%, transparent),`;
}

const highestId = ref(3);

// A CSS property can not be updated or re-registered in JS
// that means a new property with a new ID has to be created
// @dev creates a gradient with the same options and a new ID
function changeAnimations(id) {
  const keyFrames = document.createElement("style");
  let newGradient;

  const gradient = gradients.value.find((g) => g.id === id);

  newGradient = {
    id: highestId.value,
    color: gradient.color,
    xPosition: gradient.xPosition,
    yPosition: gradient.yPosition,
    strength: gradient.strength,
    hidden: gradient.hidden,
    keypoints: gradient.keypoints,
  };

  if (editingKeypoint.value?.gradient?.id == gradient?.id) {
    editingKeypoint.value.gradient.id = newGradient.id;
  }

  highestId.value += 1;

  if (openKeypoint.value === id) openKeypoint.value = newGradient.id;

  gradients.value.splice(gradients.value.indexOf(gradient), 1, newGradient);

  // remove old properties from the array
  const oldProperties = properties.value.filter((element) =>
    element.includes(`--${id}-`)
  );
  oldProperties.forEach((a) => {
    const index = properties.value.indexOf(a);
    if (index > -1) properties.value.splice(index, 1);
  });

  window.CSS.registerProperty({
    name: `--${newGradient.id}-x-position`,
    syntax: "<percentage>",
    inherits: false,
    initialValue: newGradient.xPosition + "%",
  });

  window.CSS.registerProperty({
    name: `--${newGradient.id}-y-position`,
    syntax: "<percentage>",
    inherits: false,
    initialValue: newGradient.yPosition + "%",
  });

  if (!newGradient.hidden) {
    properties.value.push(`@property --${newGradient.id}-x-position {
      syntax: '<percentage>';
        inherits: false;
        initial-value: ${newGradient.xPosition}%;
      }`);
    properties.value.push(`@property --${newGradient.id}-y-position {
        syntax: '<percentage>';
          inherits: false;
          initial-value: ${newGradient.yPosition}%;
        }`);

    document.documentElement.style.setProperty(
      `--${newGradient.id}-x-position`,
      newGradient.xPosition + "%"
    );
    document.documentElement.style.setProperty(
      `--${newGradient.id}-y-position`,
      newGradient.yPosition + "%"
    );
  }

  const createStatements = (id, x, y) =>
    `--${id}-x-position: ${x}%;--${id}-y-position: ${y}%;`;

  let globalStatements = [];
  gradients.value.forEach((gradient) => {
    if (gradient.hidden) return;

    gradient.keypoints.forEach((keypoint) => {
      const statement = createStatements(
        gradient.id,
        keypoint.xPosition,
        keypoint.yPosition
      );
      if (globalStatements[keypoint.time])
        globalStatements[keypoint.time] += statement;
      else globalStatements[keypoint.time] = statement;
    });
  });

  let animation = "";

  const animationString = (keypoint, statement) =>
    `${keypoint}% {${statement}}`;

  for (const i in globalStatements) {
    animation += animationString(i, globalStatements[i]);
  }

  keyFrames.innerHTML = `
    @keyframes main {
      ${animation}
    }
    `;

  animationCSSString.value = keyFrames.innerHTML;

  document.head.appendChild(keyFrames);
}

// creates CSS variables and properties for gradients with animations
function initCSSVariables(id, x, y) {
  if (gradients.value[id].keypoints.length > 0) {
    try {
      window.CSS.registerProperty({
        name: `--${id}-x-position`,
        syntax: "<percentage>",
        inherits: false,
        initialValue: x + "%",
      });

      window.CSS.registerProperty({
        name: `--${id}-y-position`,
        syntax: "<percentage>",
        inherits: false,
        initialValue: y + "%",
      });
    } catch (e) {}

    document.documentElement.style.setProperty(`--${id}-x-position`, x + "%");
    document.documentElement.style.setProperty(`--${id}-y-position`, y + "%");
  }
}

const animationStarted = ref(false);

function updateGradient(payload) {
  if (payload.event === "drag:start" && animationsEnabledGlobally.value) {
    animationStarted.value = true;
  }
  animationsEnabledGlobally.value = false;
  const id = gradients.value.findIndex(
    (gradient) => gradient.id === payload.id
  );

  const x = payload.newPositions.x;
  if (x >= -10 && x <= 110) gradients.value[id].xPosition = x;

  const y = payload.newPositions.y;
  if (y >= -10 && y <= 110) gradients.value[id].yPosition = y;

  setTimeout(() => {
    if (animationStarted.value && payload.event === "drag:end") {
      animationsEnabledGlobally.value = true;
      animationStarted.value = false;
    }
  }, 500);
}

function changeGradient(payload) {
  gradients.value = payload;
}

function updateKeypoint(payload) {
  if (payload.event === "drag:start" && animationsEnabledGlobally.value) {
    animationStarted.value = true;
  }
  animationsEnabledGlobally.value = false;

  const id = gradients.value.findIndex(
    (gradient) => gradient.id === payload.id
  );

  const x = payload.newPositions.x;
  if (x >= -10 && x <= 110)
    gradients.value[id].keypoints[payload.keypoint].xPosition = x;

  const y = payload.newPositions.y;
  if (y >= -10 && y <= 110)
    gradients.value[id].keypoints[payload.keypoint].yPosition = y;

  setTimeout(() => {
    if (animationStarted.value && payload.event === "drag:end") {
      animationsEnabledGlobally.value = true;
      animationStarted.value = false;
    }
  }, 500);
}

const editingKeypoint = ref({});

function editKeypoint(obj) {
  animationsEnabledGlobally.value = true;
  editingKeypoint.value = obj;
}
</script>

<template>
  <header
    class="bg-white w-5/6 m-auto flex justify-between font-bold"
    style="height: 10vh"
  >
    <div class="flex items-center">
      <img src="https://via.placeholder.com/66x66" alt="Gradient Tool" />
      <p class="ml-6 text-2xl">Gradient Tool</p>
    </div>
    <div class="flex items-center">
      <p class="text-gray-600 mr-8 cursor-pointer">
        <a href="#explore"> Explore </a>
      </p>
      <a href="#creator">
        <button
          class="
            bg-gradient-to-tr
            btn
            text-white
            hover:shadow
            px-2
            py-3
            rounded-lg
          "
        >
          <p class="font-bold">Create Gradients</p>
        </button>
      </a>
    </div>
  </header>
  <main class="grid place-items-center" id="gradient-hero" style="height: 40vh">
    <div class="font-black text-white">
      <h1 class="text-4xl">Gradients play a big part in modern web design.</h1>
      <h2 class="text-5xl mt-4">
        Create and animate them using
        <span class="text-transparent bg-clip-text bg-gradient-to-tl btn"
          >Gradient Tool</span
        >
      </h2>
      <div>
        <a href="#creator">
          <button
            class="
              font-bold
              px-5
              py-2
              rounded-lg
              mr-10
              mt-10
              hover:shadow
              bg-gradient-to-tr
              btn
            "
          >
            <p>Start Creating</p>
          </button>
        </a>
        <a href="#explore">
          <button
            class="font-bold border-2 px-5 py-2 rounded-lg hover:shadow-md"
          >
            <p>Explore</p>
          </button>
        </a>
      </div>
    </div>
  </main>
  <div class="bg-white flex items-start">
    <main class="w-full mx-32">
      <h1
        class="mt-20 text-left font-bold text-4xl"
        :class="[screenSize === 'large' ? 'mb-12' : '']"
        id="creator"
      >
        Gradient creator
      </h1>
      <div
        class="w-full flex flex-wrap justify-around items-center"
        :class="[screenSize === 'large' ? 'flex-col' : 'flex-row']"
      >
        <GradientCanvas
          :screen-size="screenSize"
          :gradients="gradients"
          :css-string="cssString()"
          :editing-keypoint="editingKeypoint"
          @move-point="updateGradient"
          @move-keypoint="updateKeypoint"
        />

        <div class="flex mt-12">
          <GradientSettings
            class="mr-12"
            :gradients="gradients"
            @change-gradient="changeGradient"
            :max-id-global="maxIdGlobal"
            @update-max-id="(newId) => (maxIdGlobal = newId)"
            @disable-animation="animationsEnabledGlobally = false"
            v-model:bg-color="bgColor"
          />

          <AnimationSettings
            :gradients="gradients"
            :editing-keypoint="editingKeypoint"
            v-model:gradientSelected="openKeypoint"
            v-model:animationsEnabled="animationsEnabledGlobally"
            v-model:animationTime="animationTime"
            v-model:animationEasing="animationEasing"
            @update-animation="changeAnimations"
            @edit-keypoint="editKeypoint"
          />
        </div>
      </div>
      <div class="mt-8 mr-16 py-4 flex items-center justify-end">
        <select
          class="bg-gray-700 text-white py-2 px-4 font-bold rounded-lg"
          v-model="screenSize"
        >
          <option value="normal">Normal</option>
          <option value="large">Large</option>
        </select>
        <CSSModal
          :css-string="cssString()"
          v-model:animations-enabled="animationsEnabledGlobally"
          :properties="properties"
          :animation-css-string="animationCSSString"
          :animation-duration="animationTimeCss"
          :animation-easing="animationEasing"
        />
      </div>
      <h1 class="mt-20 text-left font-bold text-4xl" id="explore">
        Pre-made Gradients
      </h1>
      <GradientLibrary class="mt-12" />
    </main>
  </div>
</template>

<style>
html {
  scroll-behavior: smooth;
}

#app {
  font-family: "Inter", "Avenir", "Helvetica", "Arial", "sans-serif";
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: rgb(51 65 85);
}

.btn {
  @apply from-lime-200 to-cyan-400;
}

#gradient {
  animation-name: main;
  animation-iteration-count: infinite;
  animation-duration: v-bind(animationTimeCss);
  transition-timing-function: v-bind(animationEasing);
}

#gradient-hero {
  background: radial-gradient(
      100% 100% at var(--hero-1-x-position) var(--hero-1-y-position),
      #3cddb4 0%,
      transparent
    ),
    radial-gradient(
      100% 100% at var(--hero-2-x-position) var(--hero-2-y-position),
      #2773ec 0%,
      transparent
    ),
    #2b2a41;
  animation-name: hero;
  animation-iteration-count: infinite;
  animation-duration: 6s;
  transition-timing-function: ease-in-out;
}

@property --hero-1-x-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 11.406250000000002%;
}

@property --hero-1-y-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 87.421875%;
}

@property --hero-2-x-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 80%;
}

@property --hero-2-y-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 20%;
}

@keyframes hero {
  25% {
    --hero-1-x-position: 16.718750000000004%;
    --hero-1-y-position: 16.2890625%;
    --hero-2-x-position: 86.1328125%;
    --hero-2-y-position: 83.3984375%;
  }
  50% {
    --hero-1-x-position: 83.3984375%;
    --hero-1-y-position: 15.117187500000002%;
    --hero-2-x-position: 16.718750000000004%;
    --hero-2-y-position: 84.6484375%;
  }
}

.noselect {
  -webkit-touch-callout: none;
  -webkit-user-select: none;
  -khtml-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}
</style>

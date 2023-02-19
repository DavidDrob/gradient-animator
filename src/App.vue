<script setup>
import { ref, watch, onMounted, computed } from "vue";
import GradientCanvas from "./components/GradientCanvas.vue";
import GradientSettings from "./components/GradientSettings.vue";
import AnimationSettings from "./components/AnimationSettings.vue";
import CSSModal from "./components/CSSModal.vue";
import GradientLibrary from "./components/GradientLibrary.vue";

const bgColor = ref("#1b253b");

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
        xPosition: 15,
        yPosition: 15,
        time: 25,
      },
      {
        xPosition: 80,
        yPosition: 15,
        time: 50,
      },
    ],
  },
  {
    id: 1,
    color: "#00FAA7",
    xPosition: 80,
    yPosition: 20,
    strength: 0,
    hidden: false,
    keypoints: [
      {
        xPosition: 85,
        yPosition: 80,
        time: 25,
      },
      {
        xPosition: 15,
        yPosition: 85,
        time: 50,
      },
    ],
  },
]);

const animationCSSString = ref("");
const properties = ref([]);
const rootVariables = ref([]);
const animationTime = ref(2);
const animationTimeCss = computed(() => {
  return animationTime.value + "s";
});
const animationEasing = ref("ease-in");
const screenSize = ref("normal");

const animationsEnabledGlobally = ref(false);
const openKeypoint = ref(false);
const highestId = ref(4);

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
  if (gradients.value.length) highestId.value = index + 1; // to avoid same ID error
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

  if (!newGradient.hidden) {
    rootVariables.value.push({
      key: `--${newGradient.id}-x-position`,
      value: `${newGradient.xPosition}%`,
    });

    rootVariables.value.push({
      key: `--${newGradient.id}-y-position`,
      value: `${newGradient.yPosition}%`,
    });

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
    class="w-11/12 m-auto flex items-center justify-between font-bold"
    style="height: 10vh"
  >
    <div class="flex items-center">
      <img class="w-16 h-16" src="./assets/logo.svg" alt="Gradient Animator" />
      <p class="mx-6 text-2xl hidden md:inline">Gradient Animator</p>
      <a
        href="https://www.producthunt.com/posts/gradient-animator-2?utm_source=badge-featured&utm_medium=badge&utm_souce=badge-gradient&#0045;animator&#0045;2"
        target="_blank"
        ><img
          src="https://api.producthunt.com/widgets/embed-image/v1/featured.svg?post_id=380239&theme=light"
          alt="Gradient&#0032;Animator - Create&#0032;and&#0032;animate&#0032;gradients&#0032;and&#0032;get&#0032;the&#0032;CSS&#0032;code&#0032;right&#0032;away | Product Hunt"
          style="width: 160px; height: 40px"
          width="160"
          height="40"
      /></a>
    </div>
    <div class="flex items-center">
      <p class="text-gray-600 mr-8 cursor-pointer">
        <a href="#explore"> Explore </a>
      </p>

      <a href="#creator">
        <button
          class="text-white bg-cyan-400 hover:shadow px-2 py-3 rounded-lg"
        >
          <p class="font-bold">Create Gradients</p>
        </button>
      </a>
    </div>
  </header>
  <!--
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
  </main>-->
  <div class="bg-white w-full flex items-start">
    <main class="w-5/6 m-auto md:mx-32">
      <h1
        class="my-8 md:mb-0 md:mt-20 text-left font-bold text-4xl"
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
          class="md:mt-12"
          :screen-size="screenSize"
          :gradients="gradients"
          :css-string="cssString()"
          :editing-keypoint="editingKeypoint"
          @move-point="updateGradient"
          @move-keypoint="updateKeypoint"
        />
        <select
          class="
            bg-gray-700
            text-white
            mt-8
            py-2
            px-4
            font-bold
            rounded-lg
            inline
            md:hidden
          "
          v-model="screenSize"
        >
          <option value="normal">Normal View</option>
          <option value="large">Large View</option>
        </select>

        <div class="flex flex-col md:flex-row mt-12">
          <GradientSettings
            class="mb-12 md:mr-12 md:mb-0"
            :gradients="gradients"
            @change-gradient="changeGradient"
            :max-id-global="highestId"
            @update-max-id="(newId) => (highestId = newId)"
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
      <div class="mt-8 mr-4 md:mr-16 py-4 flex items-center justify-end">
        <select
          class="
            bg-gray-700
            text-white
            py-2
            px-4
            font-bold
            rounded-lg
            hidden
            md:inline
          "
          v-model="screenSize"
        >
          <option value="normal">Normal View</option>
          <option value="large">Large View</option>
        </select>
        <div class="ml-10">
          <CSSModal
            :css-string="cssString()"
            animation-name="main"
            v-model:animations-enabled="animationsEnabledGlobally"
            :properties="properties"
            :root-variables="rootVariables"
            :animation-css-string="animationCSSString"
            :animation-duration="animationTimeCss"
            :animation-easing="animationEasing"
          />
        </div>
      </div>
      <h1 class="mt-20 text-left font-bold text-3xl md:text-4xl" id="explore">
        Pre-made Gradients
      </h1>
      <GradientLibrary class="mt-12" />
      <footer class="grid place-items-center my-6">
        <p>
          <a href="https://github.com/DavidDrob/gradient-animator">github</a> |
          <a href="https://www.producthunt.com/posts/gradient-animator-2"
            >producthunt</a
          >
          |
          <a href="https://twitter.com/0xl1ght">twitter</a>
        </p>
      </footer>
    </main>
  </div>
</template>

<style>
:root {
  --hero-1-x-position: 10%;
  --hero-1-y-position: 85%;
  --hero-2-x-position: 80%;
  --hero-2-y-position: 20%;
}

html {
  scroll-behavior: smooth;
}

#app {
  font-family: "Inter", "Avenir", "Helvetica", "Arial", "sans-serif";
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
  transition-timing-function: ease-in;
}

@property --hero-1-x-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 10%;
}

@property --hero-1-y-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 85%;
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
    --hero-1-x-position: 15%;
    --hero-1-y-position: 15%;
    --hero-2-x-position: 85%;
    --hero-2-y-position: 80%;
  }
  50% {
    --hero-1-x-position: 80%;
    --hero-1-y-position: 15%;
    --hero-2-x-position: 15%;
    --hero-2-y-position: 85%;
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

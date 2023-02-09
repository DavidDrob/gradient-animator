<script setup>
import { ref, watch, onMounted } from "vue";
import GradientCanvas from "./components/GradientCanvas.vue";
import GradientSettings from "./components/GradientSettings.vue";
import AnimationSettings from "./components/AnimationSettings.vue";

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
        xPosition: 100,
        yPosition: 20,
        time: 48,
      },
    ],
  },
]);

const animationCSSString = ref("");
const properties = ref([]);

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
  return baseString + bgColor.value;
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
</script>

<template>
  <header
    class="bg-white w-5/6 m-auto flex justify-between font-bold"
    style="height: 10vh"
  >
    <div class="flex items-center">
      <img src="https://via.placeholder.com/66x66" alt="Gradient Tool" />
      <p class="ml-4">Gradient Tool</p>
    </div>
    <div class="flex items-center">
      <p class="text-gray-600 mr-4 cursor-pointer">Explore</p>
      <button
        class="bg-cyan-400 hover:bg-cyan-500 text-white px-2 py-3 rounded-md"
      >
        <p class="font-bold">Create Gradients</p>
      </button>
    </div>
  </header>
  <main
    class="grid place-items-center"
    id="gradient"
    :style="cssString()"
    style="height: 40vh"
  >
    <div class="font-bold text-white">
      <h1 class="text-5xl">Gradients play a big part in modern web design.</h1>
      <h2 class="text-4xl mt-4">
        Create and animate them using
        <span class="text-cyan-400">Gradient Tool</span>
      </h2>
      <div>
        <button
          class="
            font-bold
            px-5
            py-2
            rounded-md
            mr-10
            mt-10
            hover:shadow-md
            bg-cyan-400
          "
        >
          <p>Start Creating</p>
        </button>
        <button
          class="
            font-bold
            border-2 border-cyan-400
            px-5
            py-2
            rounded-md
            hover:shadow-md
          "
        >
          <p>Explore</p>
        </button>
      </div>
    </div>
  </main>
  <div class="bg-white flex items-start">
    <div class="w-full flex flex-wrap justify-around items-center mx-32 mt-20">
      <GradientCanvas
        :gradients="gradients"
        :css-string="cssString()"
        @move-point="updateGradient"
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
          v-model:gradientSelected="openKeypoint"
          v-model:animationsEnabled="animationsEnabledGlobally"
          @update-animation="changeAnimations"
        />
      </div>
    </div>
  </div>
  <div class="w-full mt-32 text-white font-semibold bg-slate-600 px-4">
    <div>
      <span>Copy the CSS</span> <br />
      <span>{{ cssString() }};</span>
    </div>
    <div v-if="animationCSSString">
      <span>Copy the CSS</span> <br />
      <span v-for="property in properties" :key="property">{{ property }}</span>
      <br />
      <span>{{ animationCSSString }}</span> <br />
      <span>Add `animation-name: main;` to an element you want to animate</span>
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

#gradient {
  animation-name: main;
  animation-iteration-count: infinite;
  animation-duration: 2s;
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

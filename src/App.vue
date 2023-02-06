<script setup>
import { ref, watch, onMounted } from "vue";
import GradientCanvas from "./components/GradientCanvas.vue";
import GradientSettings from "./components/GradientSettings.vue";
import AnimationSettings from "./components/AnimationSettings.vue";

const bgColor = ref("#112521");

const gradients = ref([
  {
    id: 0,
    color: "#26d723",
    xPosition: 80,
    yPosition: 80,
    strength: 0,
    hidden: false,
    keypoints: [
      {
        xPosition: 20,
        yPosition: 20,
        time: 40,
      },
      {
        xPosition: 100,
        yPosition: 80,
        time: 80,
      },
      {
        xPosition: 0,
        yPosition: 100,
        time: 100,
      },
    ],
  },
  {
    id: 1,
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
    id: 2,
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

// A CSS property can not be updated or re-registered in JS
// that means a new property with a new ID has to be created
// @dev creates a gradient with the same options and a new ID
function changeAnimations(id) {
  const keyFrames = document.createElement("style");
  let newGradient;

  const gradient = gradients.value.find((g) => g.id === id);
  const oldPosition = gradients.value.indexOf(gradient);

  newGradient = {
    id: Math.max(...gradients.value.map((o) => o.id)) + 1,
    color: gradient.color,
    xPosition: gradient.xPosition,
    yPosition: gradient.yPosition,
    strength: gradient.strength,
    hidden: gradient.hidden,
    keypoints: gradient.keypoints,
  };

  if (openKeypoint.value === id) openKeypoint.value = newGradient.id;

  removeGradient(id);
  gradients.value.push(newGradient);

  properties.value = [];

  // gradients.value = applyDrag(gradients.value, {
  //   addedIndex: oldPosition,
  //   removedIndex: gradients.value.length - 1,
  //   payload: undefined,
  // });

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

  const createStatements = (id, x, y) =>
    `--${id}-x-position: ${x}%;--${id}-y-position: ${y}%;`;

  let globalStatements = [];
  gradients.value.forEach((gradient) => {
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
    if (animationStarted.value && payload.event === "drag:end")
      animationsEnabledGlobally.value = true;
  }, 500);
}

function changeGradient(payload) {
  gradients.value = payload;
}
</script>

<template>
  <div class="bg-slate-900 h-screen flex items-start">
    <div class="w-full flex justify-between items-center mx-32 mt-20">
      <GradientCanvas
        :gradients="gradients"
        :css-string="cssString()"
        @move-point="updateGradient"
      />

      <GradientSettings
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
  <div class="-mt-12 w-full text-white font-semibold bg-slate-600 px-4">
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
</style>

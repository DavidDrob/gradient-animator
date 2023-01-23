<script setup>
import { ref, watch, onMounted } from "vue";
import Settings from "./components/Settings.vue";
import VueResizable from "vue-resizable";
import { Container, Draggable } from "vue-dndrop";

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
const animationsEnabledGlobally = ref(false);
const openKeypoint = ref(false);

watch(animationsEnabledGlobally, async (newValue, oldValue) => {
  let ids = [];
  if (newValue) {
    gradients.value.forEach((gradient) => {
      ids.push(gradient.id);
    });
    ids.forEach((id) => {
      changeAnimations(id);
    });
  }
});

onMounted(() => {
  gradients.value.forEach((gradient) => {
    initCSSVariables(gradient.id, gradient.xPosition, gradient.yPosition);
  });
});

function createGradient() {
  gradients.value.push({
    id: Math.max(...gradients.value.map((o) => o.id)) + 2,
    color: "#23d726",
    xPosition: 80,
    yPosition: 80,
    strength: 0,
    hidden: false,
    keypoints: [
      {
        xPosition: 100,
        yPosition: 20,
        time: 2,
      },
    ],
  });
}

function removeGradient(index) {
  const gradient = gradients.value.findIndex(
    (gradient) => gradient.id == index
  );
  gradients.value.splice(gradient, 1);
}

function hideGradient(index) {
  const gradient = gradients.value.findIndex(
    (gradient) => gradient.id == index
  );
  gradients.value[gradient].hidden = !gradients.value[gradient].hidden;
}

function createKeypoint(id) {
  const gradientId = gradients.value.find((g) => g.id === id);
  gradientId.keypoints.push({
    xPosition: 80,
    yPosition: 20,
    time: 5,
  });
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

  gradients.value = applyDrag(gradients.value, {
    addedIndex: oldPosition,
    removedIndex: gradients.value.length - 1,
    payload: undefined,
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

// TODO: adjust to canvas size
const width = ref(384);
const height = ref(384);

function positionHandler(e) {
  const element = parseInt(e.cmp.dragElements[0].id);
  const id = gradients.value.findIndex((gradient) => gradient.id === element);

  const newX = (e.left / width.value) * 100;
  const newY = (e.top / height.value) * 100;

  // TODO: make this cleaner
  if (newX >= -10 && newX <= 110) gradients.value[id].xPosition = newX;
  // else if (newX <= 110) gradients.value[id].xPosition = -5;
  // else if (newX >= -10) gradients.value[id].xPosition = 105;
  if (newY >= -10 && newY <= 110) gradients.value[id].yPosition = newY;
  // else if (newY <= 110) gradients.value[id].yPosition = -5;
  // else if (newY >= -10) gradients.value[id].yPosition = 105;
}

// --- DRAG AND DROP ---
function applyDrag(arr, dragResult) {
  const { removedIndex, addedIndex, payload } = dragResult;
  if (removedIndex === null && addedIndex === null) return arr;

  const result = [...arr];
  let itemToAdd = payload;

  if (removedIndex !== null) {
    itemToAdd = result.splice(removedIndex, 1)[0];
  }

  if (addedIndex !== null) {
    result.splice(addedIndex, 0, itemToAdd);
  }

  return result;
}

function onDrop(dropResult) {
  gradients.value = applyDrag(gradients.value, dropResult);
}
</script>

<template>
  <div class="bg-slate-900 h-screen flex items-start">
    <div class="w-full flex justify-between items-center mx-32 mt-20">
      <div class="p-8 border-2 border-dashed border-gray-500/50">
        <div class="w-96 h-96 border border-gray-500 relative">
          <div class="w-96 h-96" id="gradient" :style="cssString()">
            <vue-resizable
              v-for="gradient in gradients"
              :key="gradient.id"
              dragSelector=".drag-container"
              :active="[]"
              :fit-parent="false"
              style="width: 1px; height: 1px"
              :left="(gradient.xPosition / 100) * width"
              :top="(gradient.yPosition / 100) * height"
              @drag:move="positionHandler"
            >
              <div class="drag-container" :id="gradient.id">
                <img
                  src="./assets/circle.svg"
                  style="
                    user-drag: none;
                    user-select: none;
                    transform: scale(10, 10);
                  "
                  alt="point"
                />
              </div>
            </vue-resizable>
          </div>
        </div>
      </div>

      <div class="bg-slate-900">
        <div
          class="
            w-full
            px-16
            py-4
            h-[36rem]
            rounded-md
            bg-gray-700
            text-white
            overflow-auto
            flex flex-col flex-nowrap
          "
        >
          <Container
            @drop="onDrop"
            drag-class="opacity-ghost"
            drop-class="opacity-ghost-drop"
            drag-handle-selector=".column-drag-handle"
          >
            <Draggable v-for="(gradient, index) in gradients" :key="index">
              <div class="flex items-center py-4 first:pt-0">
                <span
                  class="
                    column-drag-handle
                    cursor-pointer
                    bg-slate-900
                    p-6
                    rounded-l-md
                  "
                  >&#x2630;</span
                >
                <div class="flex bg-slate-800 rounded-r-md pr-4">
                  <div class="flex">
                    <div class="flex flex-col px-6">
                      <span
                        class="bg-green-400 px-2 my-2 rounded-md"
                        @click="removeGradient(gradient.id)"
                        >remove</span
                      >
                      <span
                        class="bg-green-400 px-2 mb-2 rounded-md"
                        @click="hideGradient(gradient.id)"
                        >{{ gradient.hidden ? "unhide" : "hide" }}</span
                      >
                    </div>
                    <div class="grid place-items-center">
                      <input type="color" v-model="gradient.color" />
                      <h1>{{ gradient.color }}</h1>
                    </div>
                  </div>
                  <div class="flex flex-col justify-center">
                    <input
                      type="range"
                      :min="-100"
                      :max="75"
                      v-model="gradient.strength"
                    />
                  </div>
                </div>
              </div>
            </Draggable>
            <div class="flex flex-col items-center py-2">
              <div class="flex flex-col bg-slate-800 w-full rounded-md py-2">
                <p>Background Color</p>
                <div class="w-full flex justify-center">
                  <input class="mr-4" type="color" v-model="bgColor" />
                  <h1>{{ bgColor }}</h1>
                </div>
              </div>
              <div class="w-full mt-4 flex justify-center">
                <button
                  class="py-2 bg-green-400 w-1/2 rounded-md"
                  @click="createGradient"
                >
                  Add
                </button>
              </div>
            </div>
          </Container>
        </div>
      </div>

      <div class="bg-slate-900">
        <div
          class="
            w-full
            px-16
            py-4
            h-[36rem]
            rounded-md
            bg-gray-700
            text-white
            overflow-auto
            flex flex-col flex-nowrap
          "
        >
          <!-- <p class="text-red-500">
            Experimental feature - doesn't work in Safari, Firefox
          </p> -->
          <div class="flex">
            <input type="checkbox" v-model="animationsEnabledGlobally" />
            <p class="pl-4">animations enabled</p>
          </div>
          <div
            class="my-2 bg-slate-800 rounded-md"
            v-for="gradient in gradients"
            :key="gradient.id"
          >
            <div
              v-if="openKeypoint === false"
              @click="openKeypoint = gradient.id"
              class="
                flex
                justify-between
                px-4
                py-2
                w-full
                rounded-md
                cursor-pointer
              "
            >
              <div
                class="w-6 h-6 rounded-sm"
                :style="'background-color:' + gradient.color"
              ></div>
              edit keyframes
            </div>
          </div>
          <div
            v-if="openKeypoint || openKeypoint === 0"
            class="pb-4 bg-slate-600 px-6 pt-3 rounded-md"
          >
            <i @click="openKeypoint = false">close</i>
            <div
              v-for="(keypoint, index) in gradients.find(
                (g) => g.id === openKeypoint
              ).keypoints"
              :key="index"
              class="mb-6"
            >
              <div class="bg-slate-800 rounded-md py-2 px-4 mb-3">
                <p>
                  X-Position:
                  <span>
                    <input
                      type="number"
                      class="bg-slate-900 pl-1 rounded-sm"
                      v-model="keypoint.xPosition"
                      @change="changeAnimations(openKeypoint)"
                      min="1"
                      max="100"
                  /></span>
                </p>
              </div>
              <div class="bg-slate-800 rounded-md py-2 px-4">
                <p>
                  Y-Position:
                  <span>
                    <input
                      type="number"
                      class="bg-slate-900 pl-1 rounded-sm"
                      v-model="keypoint.yPosition"
                      @change="changeAnimations(openKeypoint)"
                      min="1"
                      max="100"
                  /></span>
                </p>
              </div>
            </div>
            <button
              class="py-2 mt-4 bg-green-400 w-full rounded-md"
              @click="createKeypoint(openKeypoint)"
            >
              Add Keypoint
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
  <div class="-mt-12 w-full text-white font-semibold bg-slate-600 px-4">
    <span>Copy the CSS</span> <br />
    <span>{{ cssString() }}</span>
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

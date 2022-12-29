<script setup>
import { ref, computed } from "vue";
import Settings from "./components/Settings.vue";
import VueResizable from "vue-resizable";
import { Container, Draggable } from "vue-dndrop";

const bgColor = ref("#112521");

const gradients = ref([
  {
    id: 0,
    color: "#26d723",
    xPosition: 20,
    yPosition: 20,
    strength: 0,
    hidden: false,
  },
  {
    id: 1,
    color: "#3cc2dd",
    xPosition: 20,
    yPosition: 80,
    strength: 0,
    hidden: false,
  },
]);

function createGradient() {
  gradients.value.push({
    id: Math.max(...gradients.value.map((o) => o.id)) + 2,
    color: "#23d726",
    xPosition: 80,
    yPosition: 80,
    strength: 0,
    hidden: false,
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

const cssString = () => {
  var baseString = "background: ";
  gradients.value.forEach((gradient) => {
    if (gradient.hidden) return;
    baseString += createCSS(
      gradient.xPosition,
      gradient.yPosition,
      gradient.color,
      gradient.strength
    );
  });
  // remove comma at the end of the string
  return baseString + bgColor.value;
};

function createCSS(x, y, color, strength) {
  return `radial-gradient(100% 100% at ${parseInt(x)}% ${parseInt(
    y
  )}%, ${color} ${strength}%, transparent),`;
}

// TODO: adjust to canvas size
const width = ref(384);
const height = ref(384);

function positionHandler(e) {
  console.log(e);
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
        <div class="w-96 h-96 border-2 border-gray-500 relative">
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
            bg-gray-700
            text-white
            flex flex-col flex-wrap
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
            <div class="flex items-center py-2">
              <div class="flex flex-col bg-slate-800 w-full rounded-md py-2">
                <p>Background Color</p>
                <div class="w-full flex justify-center">
                  <input class="mr-4" type="color" v-model="bgColor" />
                  <h1>{{ bgColor }}</h1>
                </div>
              </div>
            </div>
          </Container>
          <div class="mt-auto flex justify-center">
            <button
              class="py-2 bg-green-400 w-1/2 rounded-md"
              @click="createGradient"
            >
              Add
            </button>
          </div>
        </div>
      </div>
    </div>
    <div
      class="
        absolute
        bottom-2
        w-full
        text-white
        font-semibold
        bg-slate-600
        px-4
      "
    >
      <span>Copy the CSS</span> <br />
      <span>{{ cssString() }}</span>
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

/* @keyframes example {
  from {
    background-color: red;
  }
  to {
    background-color: yellow;
  }
}

#gradient {
  animation-name: example;
  animation-duration: 4s;
} */
</style>

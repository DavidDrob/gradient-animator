<script setup>
import { computed, defineEmits, defineProps } from "vue";
import { Container, Draggable } from "vue-dndrop";
import trash from "../assets/trash.svg";
import eye from "../assets/eye.svg";
import eyeSlash from "../assets/eye-slash.svg";

const props = defineProps({
  gradients: Array,
  maxIdGlobal: Number,
  bgColor: { type: String, default: "#112521" },
});

const emit = defineEmits([
  "update:bg-color",
  "change-gradient",
  "update-max-id",
  "disable-animation",
]);

function removeGradient(index) {
  const gradient = props.gradients.findIndex(
    (gradient) => gradient.id == index
  );
  if (props.gradients.length)
    emit("update-max-id", index + props.gradients.length); // to avoid same ID error

  props.gradients.splice(gradient, 1);
  emit("change-gradient", props.gradients);
  emit("disable-animation");
}

function hideGradient(index) {
  const gradient = props.gradients.findIndex(
    (gradient) => gradient.id == index
  );

  if (props.gradients[gradient].hidden) emit("disable-animation");

  props.gradients[gradient].hidden = !props.gradients[gradient].hidden;
  emit("change-gradient", props.gradients);
}

function createGradient() {
  props.gradients.push({
    id: props.maxIdGlobal,
    color: "#23d726",
    xPosition: 80,
    yPosition: 80,
    strength: 0,
    hidden: false,
    keypoints: [
      {
        xPosition: 20,
        yPosition: 20,
        time: 50,
      },
    ],
  });

  emit("update-max-id", props.maxIdGlobal + 1);
  emit("disable-animation");
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
  emit("change-gradient", applyDrag(props.gradients, dropResult));
}
</script>

<template>
  <div
    class="
      p-4
      md:px-8
      h-[36rem]
      rounded-xl
      bg-gray-700
      text-white
      overflow-auto
      flex flex-col flex-nowrap
    "
  >
    <p class="text-2xl text-center font-semibold mb-4">Gradient Settings</p>
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
              bg-gray-900
              py-9
              px-5
              md:py-7 md:px-6
              rounded-l-md
              text-xl
            "
            >&#x2630;</span
          >
          <div class="flex bg-gray-800 w-full rounded-r-md px-4">
            <div class="flex justify-around w-full">
              <div class="flex my-4 md:my-0 md:mx-4 md:ml-0">
                <img
                  class="w-5 mr-4 cursor-pointer"
                  @click="removeGradient(gradient.id)"
                  :src="trash"
                  alt="remove"
                />
                <img
                  class="w-5 cursor-pointer"
                  @click="hideGradient(gradient.id)"
                  :src="gradient.hidden ? eye : eyeSlash"
                  alt="hide"
                />
              </div>
              <div class="py-2 md:py-3 flex flex-col md:flex-row">
                <div class="grid place-items-center pb-2 md:pb-0">
                  <input
                    class="w-8 h-8 mb-1 bg-transparent"
                    id="color"
                    type="color"
                    v-model="gradient.color"
                  />
                  <h1 class="font-thin">{{ gradient.color.toUpperCase() }}</h1>
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
          </div>
        </div>
      </Draggable>
      <div class="flex flex-col items-center py-2">
        <div class="flex flex-col bg-gray-800 w-full rounded-xl py-2">
          <p class="font-light text-center mb-2">Background Color</p>
          <div class="w-full flex justify-center items-center">
            <input
              class="mr-4 w-8 h-8 bg-transparent"
              id="color"
              type="color"
              :value="bgColor"
              @input="$emit('update:bg-color', $event.target.value)"
            />
            <p class="font-thin">{{ bgColor.toUpperCase() }}</p>
          </div>
        </div>
        <div class="w-full mt-4 flex justify-center">
          <button
            class="py-2 bg-cyan-400 w-1/3 rounded-lg"
            @click="createGradient"
          >
            Add
          </button>
        </div>
      </div>
    </Container>
  </div>
</template>

<style scoped>
#color {
  -webkit-appearance: none;
  padding: 0;
  border: none;
  border-radius: 0.375rem;
}
#color::-webkit-color-swatch {
  border: none;
  border-radius: 0.375rem;
  padding: 0;
}
#color::-webkit-color-swatch-wrapper {
  border: none;
  border-radius: 0.375rem;
  padding: 0;
}
</style>
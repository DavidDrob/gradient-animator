<script setup>
import { computed, defineEmits, defineProps } from "vue";
import chveronRight from "../assets/chevron-right.svg";
import chveronDown from "../assets/chevron-down.svg";

const props = defineProps({
  gradients: Array,
  animationsEnabled: Boolean,
  animationTime: Number,
  animationEasing: String,
  gradientSelected: { default: false },
});

const emit = defineEmits([
  "update:animationsEnabled",
  "update:animationTime",
  "update:animationEasing",
  "update-animation",
  "update:gradientSelected",
]);

const animationTime = computed({
  get() {
    return props.animationTime;
  },
  set(value) {
    emit("update:animationTime", value);
  },
});

const animationEasing = computed({
  get() {
    return props.animationEasing;
  },
  set(value) {
    emit("update:animationEasing", value);
  },
});

const animationToggled = computed({
  get() {
    return props.animationsEnabled;
  },
  set(value) {
    emit("update:animationsEnabled", value);
  },
});

const gradientSelected = computed({
  get() {
    return props.gradientSelected;
  },
  set(value) {
    emit("update:gradientSelected", value);
  },
});

// sort keypoints with a 75ms delay after the user changes time of a keypoint
function changeTime(id) {
  const gradient = props.gradients.find((g) => g.id === id);
  setTimeout(() => {
    gradient.keypoints.sort((a, b) => a.time - b.time);
  }, 750);

  emit("update-animation", gradientSelected.value);
}

function createKeypoint(id) {
  const gradient = props.gradients.find((g) => g.id === id);
  gradient.keypoints.push({
    xPosition: 80,
    yPosition: 20,
    time: 90,
  });
  gradient.keypoints.sort((a, b) => {
    return a.time - b.time;
  });
}

const openGradient = computed(() => {
  return props.gradients.find((g) => g.id == props.gradientSelected);
});
</script>

<template>
  <div class="bg-slate-900 rounded-md">
    <div
      class="
        w-full
        px-8
        py-4
        h-[36rem]
        rounded-md
        bg-gray-700
        text-white
        overflow-auto
        flex flex-col flex-nowrap
      "
    >
      <p class="text-xl font-bold mb-4">Animation Settings</p>
      <!-- <p class="text-red-500">
            Experimental feature - doesn't work in Safari, Firefox
          </p> -->
      <div class="flex justify-around">
        <input
          class="h-4 w-4 border-none rounded-md my-1 cursor-pointer"
          type="checkbox"
          v-model="animationToggled"
        />
        <p class="-ml-4 font-thin">Animations Enabled</p>
      </div>
      <div>
        <div
          class="my-2 bg-slate-800 rounded-md"
          v-for="gradient in gradients"
          :key="gradient.id"
        >
          <div
            @click="
              gradientSelected === gradient.id
                ? (gradientSelected = false)
                : (gradientSelected = gradient.id)
            "
            class="flex p-4 w-full rounded-md cursor-pointer"
          >
            <div
              class="w-6 h-6 rounded-md mr-24"
              :style="'background-color:' + gradient.color"
            ></div>
            <div class="flex items-center">
              <img
                class="h-5 w-5"
                :src="
                  gradient.id === gradientSelected ? chveronDown : chveronRight
                "
                alt="open settings"
              />
              <p class="noselect">edit keyframes</p>
            </div>
          </div>
          <div v-if="gradient.id === gradientSelected">
            <div
              v-for="(keypoint, index) in openGradient.keypoints"
              :key="index"
              class="mb-6 bg-slate-700 py-4 mx-4 rounded-md"
            >
              <div class="flex justify-between mx-4">
                <input
                  type="number"
                  class="bg-slate-700 pl-1 rounded-sm"
                  v-model="keypoint.time"
                  @change="changeTime(gradientSelected)"
                  min="1"
                  max="100"
                />

                <div class="cursor-pointer">Change Position</div>
              </div>
            </div>
            <button
              class="py-2 mb-4 bg-cyan-400 w-1/2 rounded-md"
              @click="createKeypoint(gradientSelected)"
            >
              Add Keypoint
            </button>
          </div>
        </div>
        <div class="my-2 py-4 flex justify-between bg-slate-800 rounded-md">
          <div class="flex flex-col ml-4">
            <p class="font-bold">Duration</p>
            <input
              class="bg-slate-700 w-12 pl-2 mt-2 rounded-md"
              v-model="animationTime"
              type="number"
            />
          </div>
          <div class="flex flex-col w-full mx-8">
            <p class="font-bold">Easing</p>
            <select
              class="bg-slate-700 px-2 py-1 w-full mt-2 rounded-md"
              v-model="animationEasing"
            >
              <option value="ease-in-out">ease-in-out</option>
              <option value="ease-in">ease-in</option>
              <option value="ease-out">ease-out</option>
            </select>
          </div>
          <div class="flex flex-col w-12 mr-4">
            <p class="font-bold">Reset</p>
            <input
              class="h-4 w-4 rounded-md cursor-pointer mt-4"
              type="checkbox"
              v-model="animationToggled"
            />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>


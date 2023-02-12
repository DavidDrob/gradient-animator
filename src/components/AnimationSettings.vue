<script setup>
import { computed, defineEmits, defineProps } from "vue";
import chveronRight from "../assets/chevron-right.svg";
import chveronDown from "../assets/chevron-down.svg";
import trash from "../assets/trash.svg";

const props = defineProps({
  gradients: Array,
  animationsEnabled: Boolean,
  animationTime: Number,
  animationEasing: String,
  gradientSelected: { default: false },
  editingKeypoint: Object,
});

const emit = defineEmits([
  "update:animationsEnabled",
  "update:animationTime",
  "update:animationEasing",
  "update-animation",
  "update:gradientSelected",
  "edit-keypoint",
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

function removeKeypoint(gradient, keypointId) {
  gradient.keypoints.splice(keypointId, 1);
}

function createKeypoint(id) {
  const oldKeypoint =
    props.editingKeypoint?.gradient?.keypoints[props.editingKeypoint?.keypoint]
      .time;
  const gradient = props.gradients.find((g) => g.id === id);
  gradient.keypoints.push({
    xPosition: 100,
    yPosition: 100,
    time: 25,
  });
  gradient.keypoints.sort((a, b) => {
    return a.time - b.time;
  });
  props.editingKeypoint.keypoint = gradient.keypoints.findIndex(
    (k) => k.time === oldKeypoint
  );

  emit("update-animation", gradientSelected.value);
}

function changePosition(gradient, keypointId) {
  let obj;
  if (
    gradient.id != props.editingKeypoint?.gradient?.id ||
    keypointId != props.editingKeypoint.keypoint
  ) {
    obj = {
      gradient: gradient,
      keypoint: keypointId,
    };
  } else obj = {};

  emit("edit-keypoint", obj);
}

const ctaMessage = (gradient, keypointId) => {
  if (
    gradient.id === props.editingKeypoint?.gradient?.id &&
    keypointId === props.editingKeypoint.keypoint
  ) {
    return "Done editing";
  }
  return "Change Position";
};

const openGradient = computed(() => {
  return props.gradients.find((g) => g.id == props.gradientSelected);
});
</script>

<template>
  <div class="bg-gray-900 rounded-xl">
    <div
      class="
        w-full
        px-8
        py-4
        h-[36rem]
        rounded-xl
        bg-gray-700
        text-white
        overflow-auto
        flex flex-col flex-nowrap
      "
    >
      <p class="text-2xl font-semibold mb-4">Animation Settings</p>
      <div class="flex justify-center items-center w-full mb-2">
        <div class="w-fit flex">
          <p class="mr-4 font-light">Animations Enabled</p>
          <input
            class="h-4 w-4 border-none rounded-xl my-1 cursor-pointer"
            type="checkbox"
            v-model="animationToggled"
          />
        </div>
      </div>
      <div>
        <div
          class="my-2 bg-gray-800 rounded-xl"
          v-for="gradient in gradients"
          :key="gradient.id"
        >
          <div
            @click="
              gradientSelected === gradient.id
                ? (gradientSelected = false)
                : (gradientSelected = gradient.id)
            "
            class="flex p-4 w-full rounded-xl cursor-pointer"
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
              <p class="noselect font-light">edit keyframes</p>
            </div>
          </div>
          <div v-if="gradient.id === gradientSelected">
            <div
              v-for="(keypoint, index) in openGradient.keypoints"
              :key="index"
              class="mb-6 bg-gray-900 py-4 mx-4 rounded-xl"
            >
              <div class="flex items-center justify-between mx-4">
                <img
                  class="w-5 cursor-pointer"
                  @click="removeKeypoint(openGradient, index)"
                  :src="trash"
                  alt="remove"
                />
                <input
                  type="number"
                  class="
                    bg-gray-900
                    pl-1
                    ml-2
                    text-sm
                    font-light
                    rounded-sm
                    w-12
                  "
                  v-model="keypoint.time"
                  @change="changeTime(gradientSelected)"
                  min="1"
                  max="100"
                />

                <div
                  class="cursor-pointer text-sm font-light w-full text-right"
                  @click="changePosition(openGradient, index)"
                >
                  {{ ctaMessage(openGradient, index) }}
                </div>
              </div>
            </div>
            <button
              class="py-2 mb-4 btn bg-gradient-to-tr w-1/2 rounded-lg"
              @click="createKeypoint(gradientSelected)"
            >
              Add Keypoint
            </button>
          </div>
        </div>
        <div class="my-2 p-4 flex justify-between bg-gray-800 rounded-xl">
          <div class="flex flex-col">
            <p class="font-semibold">Duration</p>
            <input
              class="bg-gray-900 w-12 pl-2 mt-2 font-light rounded-lg"
              v-model="animationTime"
              type="number"
            />
          </div>
          <div class="flex flex-col ml-8">
            <p class="font-semibold text-left">Easing</p>
            <select
              class="bg-gray-900 px-2 py-1 w-full mt-2 font-light rounded-lg"
              v-model="animationEasing"
            >
              <option class="font-light" value="ease-in-out">
                ease-in-out
              </option>
              <option class="font-light" value="ease-in">ease-in</option>
              <option class="font-light" value="ease-out">ease-out</option>
            </select>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>


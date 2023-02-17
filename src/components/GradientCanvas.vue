<script setup>
import {
  computed,
  reactive,
  defineEmits,
  defineProps,
  onMounted,
  watch,
} from "vue";
import VueResizable from "vue-resizable";

const props = defineProps({
  screenSize: String,
  gradients: Array,
  cssString: String,
  editingKeypoint: Object,
});

const emit = defineEmits(["move-point", "move-keypoint"]);

onMounted(() => {
  adjustCanvasSettings();
});

watch(
  () => props.screenSize,
  (newValue, oldValue) => {
    setTimeout(() => {
      adjustCanvasSettings();
    }, 1);
  }
);

const canvasSettings = reactive({
  width: 384,
  height: 384,
});

function adjustCanvasSettings() {
  const canvas = document.querySelector("#canvas");
  canvasSettings.width = canvas.offsetWidth;
  canvasSettings.height = canvas.offsetHeight;
}

function positionHandler(e) {
  const element = parseInt(e.cmp.dragElements[0].id);

  const newX = (e.left / canvasSettings.width) * 100;
  const newY = (e.top / canvasSettings.height) * 100;

  if (props.editingKeypoint.gradient) {
    emit("move-keypoint", {
      id: props.editingKeypoint.gradient.id,
      keypoint: props.editingKeypoint.keypoint,
      newPositions: { x: newX, y: newY },
      event: e.eventName,
    });
  } else {
    emit("move-point", {
      id: element,
      newPositions: { x: newX, y: newY },
      event: e.eventName,
    });
  }
}

const gradientHandlers = computed(() => {
  const id = props.editingKeypoint?.keypoint;

  if (props.editingKeypoint.gradient) {
    return [
      {
        id: id,
        xPosition: props.editingKeypoint.gradient?.keypoints[id].xPosition,
        yPosition: props.editingKeypoint.gradient?.keypoints[id].yPosition,
      },
    ];
  }
  return props.gradients.filter((gradient) => !gradient.hidden);
});

const screenOptions = computed(() => {
  let desktopNormalMultiplier = 2;
  let desktopLargeMultiplier = 3.5;
  if (window.screen.width < 768) {
    desktopNormalMultiplier = 1;
    desktopLargeMultiplier = 1;
  }

  const options = {
    normal: `height: ${16 * desktopNormalMultiplier}rem; width: ${
      16 * desktopNormalMultiplier
    }rem`,
    large: `height: ${16 * desktopNormalMultiplier}rem; width: ${
      22 * desktopLargeMultiplier
    }rem`,
  };
  return options[props.screenSize];
});
</script>

<template>
  <div class="rounded-xl" id="gradient" :style="cssString">
    <div
      class="
        -m-3
        md:-m-6
        bg-transparent
        border-2 border-dashed border-gray-500/50
        rounded-xl
      "
      :style="screenOptions"
      id="canvas"
    >
      <vue-resizable
        v-for="gradient in gradientHandlers"
        :key="gradient.id"
        dragSelector=".drag-container"
        :active="[]"
        :width="1"
        :height="1"
        :fit-parent="true"
        style="width: 1px; height: 1px"
        :left="(gradient.xPosition / 100) * canvasSettings.width"
        :top="(gradient.yPosition / 100) * canvasSettings.height"
        @drag:start="positionHandler"
        @drag:move="positionHandler"
        @drag:end="positionHandler"
      >
        <div class="drag-container" :id="gradient.id">
          <img
            src="../assets/circle.svg"
            style="
              user-drag: none;
              user-select: none;
              transform: scale(10, 10) translate(-0.25px, -0.25px);
            "
            alt="point"
          />
        </div>
      </vue-resizable>
    </div>
  </div>
</template>


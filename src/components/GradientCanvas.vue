<script setup>
import { computed, reactive, defineEmits, defineProps, onMounted } from "vue";
import VueResizable from "vue-resizable";

const props = defineProps({
  gradients: Array,
  cssString: String,
});

const emit = defineEmits(["move-point"]);

onMounted(() => {
  const canvas = document.querySelector("#canvas");
  canvasSettings.width = canvas.offsetWidth;
  canvasSettings.height = canvas.offsetHeight;
});

const canvasSettings = reactive({
  width: 384,
  height: 384,
});

function positionHandler(e) {
  const element = parseInt(e.cmp.dragElements[0].id);

  const newX = (e.left / canvasSettings.width) * 100;
  const newY = (e.top / canvasSettings.height) * 100;

  emit("move-point", {
    id: element,
    newPositions: { x: newX, y: newY },
    event: e.eventName,
  });
}

const unhiddenGradients = computed(() => {
  return props.gradients.filter((gradient) => !gradient.hidden);
});
</script>

<template>
  <div class="rounded-md" id="gradient" :style="cssString">
    <div
      class="
        w-[32rem]
        h-[32rem]
        -m-6
        bg-transparent
        border-2 border-dashed border-gray-500/50
        rounded-md
      "
      id="canvas"
    >
      <vue-resizable
        v-for="gradient in unhiddenGradients"
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


<script setup>
import { reactive, defineEmits, defineProps } from "vue";
import VueResizable from "vue-resizable";

defineProps({
  gradients: Array,
  cssString: String,
});

const emit = defineEmits(["move-point"]);

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
</script>

<template>
  <div id="gradient" :style="cssString">
    <div
      class="
        w-[25.5rem]
        h-[25.5rem]
        -m-6
        bg-transparent
        border-2 border-dashed border-gray-500/50
      "
    >
      <vue-resizable
        v-for="gradient in gradients"
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
            style="user-drag: none; user-select: none; transform: scale(10, 10)"
            alt="point"
          />
        </div>
      </vue-resizable>
    </div>
  </div>
</template>


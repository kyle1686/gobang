<script setup>
import { ref } from "vue";
import { GridPointState, NUM_COLS, NUM_ROWS } from "../consts";

const props = defineProps({
  state: { type: Number, required: true },
  row: { type: Number, required: true },
  col: { type: Number, required: true },
});

const highlight = ref(false);

function setHighlight() {
  highlight.value = true;
}
function clearHighlight() {
  highlight.value = false;
}
</script>

<template>
  <!-- The color box where 2 lines cross -->
  <div
    :style="{
      display: 'inline-flex',
      'justify-content': 'center',
      'align-items': 'center',
      'background-color': `${highlight ? 'gold' : 'goldenrod'}`,
    }"
    @mouseenter="setHighlight"
    @mouseleave="clearHighlight"
  >
    <!-- the horizontal line -->
    <div
      :style="{
        position: 'absolute',
        'background-color': 'black',
        height: '0.1rem',
        width: `${col === 0 || col === NUM_COLS - 1 ? '50%' : '100%'}`,
        left: `${col === 0 ? '50%' : '0%'}`,
      }"
    ></div>
    <!-- the vertical line -->
    <div
      :style="{
        position: 'absolute',
        'background-color': 'black',
        width: '0.1rem',
        height: `${row === 0 || row === NUM_ROWS - 1 ? '50%' : '100%'}`,
        top: `${row === 0 ? '50%' : '0%'}`,
      }"
    ></div>
    <!-- the white or black piece placed on the board -->
    <div
      v-if="state === GridPointState.BLACK || state == GridPointState.WHITE"
      :style="{
        'background-color': `${
          state === GridPointState.BLACK ? 'black' : 'white'
        }`,
        'border-radius': '100%',
        width: '67%',
        height: '67%',
      }"
    ></div>
  </div>
</template>

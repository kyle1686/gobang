<script setup>
import { reactive, ref } from "vue";
import { GridPointState, NUM_COLS, NUM_ROWS } from "../consts";
import GridPoint from "./GridPoint.vue";

const emit = defineEmits(["gameOver"]);

const GameState = Object.freeze({
  UNKNOWN: 0,
  WHITE_WINS: 1,
  BLACK_WINS: 2,
  DRAW: 3,
});

// Use 1-d array to represent the 2-d grid to play gobang on.
const grid = reactive(
  Array.from({ length: NUM_COLS * NUM_ROWS }, () => GridPointState.UNSET)
);

const isWhitesTurn = ref(false);
const gameState = ref(GameState.UNKNOWN);
let totalMoves = 0;

function clear() {
  isWhitesTurn.value = false;
  gameState.value = GameState.UNKNOWN;
  totalMoves = 0;
  for (let i = 0; i < NUM_COLS * NUM_ROWS; i++) {
    grid[i] = GridPointState.UNSET;
  }
}

function getGameResultMsg() {
  let msg = "";
  if (gameState.value != GameState.UNKNOWN) {
    if (gameState.value == GameState.BLACK_WINS) {
      msg = "Black Wins!";
    } else if (gameState.value == GameState.WHITE_WINS) {
      msg = "White Wins!";
    } else {
      msg = "Draw!";
    }
  }
  return msg;
}

function alertGameOver() {
  if (gameState.value != GameState.UNKNOWN) {
    const msg = getGameResultMsg();
    alert("Game over: " + msg);
  }
}

// Set gameState assuming the last move is to set grid[indexInGrid]
// to either black or white.
function setGameStateAfterMove(indexInGrid) {
  const rowDiff = [0, 1, 1, 1];
  const colDiff = [1, 0, 1, -1];
  const rowStart = [0, -4, -4, -4];
  const colStart = [-4, 0, -4, 4];
  let row = getRow(indexInGrid);
  let col = getCol(indexInGrid);
  const target = grid[getGridIndexAt(row, col)];
  for (let i = 0; i < 4; i++) {
    let count = 0;
    let r = row + rowStart[i];
    let c = col + colStart[i];
    for (let j = 0; j < 9; j++) {
      if (r < 0 || r >= NUM_ROWS || c < 0 || c >= NUM_COLS) {
        r += rowDiff[i];
        c += colDiff[i];
        continue;
      }
      if (grid[getGridIndexAt(r, c)] != target) {
        count = 0;
      } else {
        count++;
        if (count > 4) {
          gameState.value =
            target === GridPointState.WHITE
              ? GameState.WHITE_WINS
              : GameState.BLACK_WINS;
          return;
        }
      }
      r += rowDiff[i];
      c += colDiff[i];
    }
  }
  gameState.value =
    totalMoves == NUM_COLS * NUM_ROWS ? GameState.DRAW : GameState.UNKNOWN;
}

// Player moves to set grid[indexInGrid] to moveState
function makeMove(indexInGrid, moveState) {
  if (gameState.value != GameState.UNKNOWN) {
    alertGameOver;
    return;
  }

  if (grid[indexInGrid] != GridPointState.UNSET) {
    return;
  }
  grid[indexInGrid] = moveState;
  totalMoves++;

  setGameStateAfterMove(indexInGrid);

  if (gameState.value == GameState.UNKNOWN) {
    isWhitesTurn.value = !isWhitesTurn.value;
  } else {
    alertGameOver();
  }
}

// Returns the row index fpr the given 1-d index indexInGrid
function getRow(indexInGrid) {
  return Math.floor(indexInGrid / NUM_COLS);
}

// Returns the column index fpr the given 1-d index indexInGrid
function getCol(indexInGrid) {
  return indexInGrid % NUM_COLS;
}

// Returns the 1-d array index for the given row and col
function getGridIndexAt(row, col) {
  return row * NUM_COLS + col;
}
</script>

<template>
  <div style="height: 2rem; width: calc(100% - 2rem); margin: auto">
    <span v-if="gameState === GameState.UNKNOWN"
      >Next move: {{ isWhitesTurn ? "White" : "Black" }}</span
    >
    <span v-if="gameState != GameState.UNKNOWN"> {{ getGameResultMsg() }}</span>
    <button type="button" @click="clear">Restart</button>
  </div>
  <div
    style="
      height: calc(100% - 2rem);
      width: calc(100% - 2rem);
      display: flex;
      justify-content: center;
      margin: auto;
    "
  >
    <div
      :style="{
        height: '100%',
        width: '100%',
        display: 'grid',
        'grid-template-columns': `repeat(${NUM_COLS}, minmax(0px, 1fr))`,
      }"
    >
      <GridPoint
        v-for="(gridPointState, index) in grid"
        :key="index"
        :state="gridPointState"
        :row="getRow(index)"
        :col="getCol(index)"
        @click="
          makeMove(
            index,
            isWhitesTurn ? GridPointState.WHITE : GridPointState.BLACK
          )
        "
        :style="{
          cursor: `${
            gridPointState === GridPointState.UNSET ? 'pointer' : 'not-allowed'
          }`,
        }"
      />
    </div>
  </div>
</template>

<style scoped>
button {
  float: left;
  background-color: coral;
  float: right;
  padding: 5px 5px;
  margin-bottom: 5px;
  margin-right: 5px;
  text-align: center;
}
</style>

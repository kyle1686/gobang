<script setup>
import Game from "./components/Game.vue";
</script>

<template>
  <header style="height: 3rem; width: 100%">
    <h1 style="text-align: center; font-style: italic; font-weight: bold">
      Gobang Game
    </h1>
  </header>

  <div style="width: 100%; height: calc(100% - 3rem)">
    <div class="gameContainer">
      <Game />
    </div>
  </div>
</template>

<style>
* {
  position: relative;
}
html,
body,
#app {
  margin: 0;
  padding: 0;
  width: 100%;
  height: 100%;
  overflow: scroll;
  background: white;
}

.gameContainer {
  width: 80vmin;
  height: 80vmin;
  justify-content: center;
  margin: auto;
}
</style>

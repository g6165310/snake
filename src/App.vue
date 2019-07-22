<template>
  <div id="app">
    <div class="container">
      <StartPage @changeStatus="change" v-if="isStart"/>
      <GamePage @changeStatus="change" @updateScore="updateScore" v-if="isPlaying"/>
      <EndPage @changeStatus="change" :score="score" :isEnd="isEnd" v-if="isEnd"/>
    </div>
  </div>
</template>

<script>
import StartPage from "./pages/StartPage";
import GamePage from "./pages/GamePage";
import EndPage from "./pages/EndPage";
export default {
  name: "app",
  components: {
    StartPage,
    GamePage,
    EndPage
  },
  data() {
    return {
      isStart: true,
      isPlaying: false,
      isEnd: false,
      score: 0
    };
  },
  methods: {
    updateScore(score) {
      this.score = score;
    },
    change(status) {
      const vm = this;
      switch (status) {
        case "game":
          vm.isStart = false;
          vm.isPlaying = true;
          vm.isEnd = false;
          break;
        case "end":
          vm.isStart = false;
          vm.isPlaying = false;
          vm.isEnd = true;
          break;
        case "start":
          vm.isStart = true;
          vm.isPlaying = false;
          vm.isEnd = false;
          break;
      }
    }
  },
  mounted() {},
  created() {
    this.bestScore = localStorage.getItem("bestScore");
  },
  updated() {
  }
};
</script>

<style lang="scss">
@import "./reset.scss";
@import url("https://fonts.googleapis.com/css?family=Press+Start+2P");
html,body{
  height: 100%;
}
#app {
  font-family: "Press Start 2P", cursive;
  width: 100%;
  min-height: 100vh;
  background-color: #000;
  display: flex;
  justify-content: center;
  align-items: center;
}
.container{
  width: 1280px;
  height: 800px;
}
</style>

<template>
  <div class="game-page">
    <span class="decorate-text">SNAA</span>
    <span class="decorate-text">SNAA</span>
    <span class="decorate-text">AAAAKE</span>
    <span class="decorate-text">AAAAKE</span>
    <div class="game-panel">
      <div class="grid" v-for="index in col*row" :key="index">
        <div
          class="overlay"
          v-if="checkSameRowCol(index)&&index!=target"
          :style="{opacity:setTargetOpacity(index)}"
        ></div>
        <div class="target" v-if="target===index">
          <div class="overlay"></div>
          <img src="../assets/ic-point.svg" alt>
        </div>
        <div
          class="snake"
          v-if="snake.indexOf(index)!=-1"
          :style="{backgroundColor:setSnakeBody(index),boxShadow:setShadow(index)}"
        ></div>
      </div>
    </div>
    <div class="current-score">
      <span>score</span>
      <span>{{score}}</span>
    </div>
    <div class="best-score">
      <span>best</span>
      <span>{{bestScore}}</span>
    </div>
  </div>
</template>

<script>
export default {
  name: "GamePage",
  data() {
    return {
      bestScore: 0,
      col: 28,
      row: 16,
      target: null,
      targetCol: null,
      targetRow: null,
      snake: [],
      snakeLength: 1,
      timer: null,
      direction: 40,
      headshadow:
        "0px 8px 15px rgba(255,255,255,0.3),0px -8px 15px rgba(255,255,255,0.3),8px 0px 15px rgba(255,255,255,0.3),-8px 0px 15px rgba(255,255,255,0.3)"
    };
  },
  computed: {
    score() {
      return this.snakeLength - 1;
    }
  },
  watch: {
    snakeLength: function() {
      this.$emit("updateScore", this.snakeLength - 1);
    }
  },
  methods: {
    setTarget() {
      const vm = this;
      let randomNum = Math.floor(Math.random() * vm.col * vm.row) + 1;
      if (vm.snake.indexOf(randomNum) != -1) {
        return vm.setTarget();
      } else {
        vm.target = randomNum;
      }
      vm.targetCol = vm.target % vm.col;
      vm.targetRow = Math.floor(vm.target / vm.col) + 1;
    },
    setInit() {
      const vm = this;
      let randomNum = Math.floor(Math.random() * vm.col * vm.row) + 1;
      if (randomNum == vm.target) {
        return vm.setInit();
      } else {
        vm.snake.push(randomNum);
      }
    },
    move() {
      const vm = this;
      let head = this.snake[0];
      switch (vm.direction) {
        case 37:
          if (head % vm.col === 1) {
            vm.snake.unshift(head + vm.col - 1);
          } else {
            vm.snake.unshift(head - 1);
          }
          break;
        case 38:
          if (head <= 28) {
            vm.snake.unshift(head + vm.col * (vm.row - 1));
          } else {
            vm.snake.unshift(head - 28);
          }
          break;
        case 39:
          if (head % vm.col === 0) {
            vm.snake.unshift(head - vm.col + 1);
          } else {
            vm.snake.unshift(head + 1);
          }
          break;
        case 40:
          if (head >= vm.col * vm.row - vm.col) {
            vm.snake.unshift(head - vm.col * (vm.row - 1));
          } else {
            vm.snake.unshift(head + 28);
          }
          break;
      }
      vm.checkMeet();
      while (this.snake.length > this.snakeLength) {
        this.snake.pop();
      }
    },
    checkMeet() {
      if (this.snakeLength != 1 && this.snake.lastIndexOf(this.snake[0]) != 0) {
        if (this.snakeLength - 1 > this.bestScore) {
          localStorage.setItem("bestScore", this.snakeLength - 1);
        }
        clearInterval(this.timer);
        this.$emit("changeStatus", "end");
      }
      if (this.snake[0] === this.target) {
        this.snakeLength++;
        this.setTarget();
      }
    },
    changeDirection(e) {
      e.preventDefault();
      if (Math.abs(e.keyCode - this.direction) == 2) {
        return;
      } else {
        this.direction = e.keyCode;
      }
    },
    checkSameRowCol(index) {
      let col = index % this.col;
      let row;
      if (index % this.col === 0) {
        row = Math.floor(index / this.col);
      } else {
        row = Math.floor(index / this.col) + 1;
      }
      if (row === this.targetRow || col === this.targetCol) {
        return 1;
      } else {
        return 0;
      }
    },
    setShadow(index) {
      if (this.snake[0] === index) {
        return this.headshadow;
      }
    },
    setSnakeBody(index) {
      if (this.snake[0] === index) {
        return "#00FFE2";
      } else {
        return `rgba(255,255,255,${10 / this.snake.indexOf(index)})`;
      }
    },
    setTargetOpacity(index) {
      let col = index % this.col;
      let row;
      if (index % this.col === 0) {
        row = Math.floor(index / this.col);
      } else {
        row = Math.floor(index / this.col) + 1;
      }
      if (col === 0) {
        col = 28;
      }
      let adjustTargetCol = this.targetCol;
      if (this.targetCol == 0) {
        adjustTargetCol = 28;
      }
      if (col === adjustTargetCol) {
        return 0.3 / (1 + Math.abs(row - this.targetRow));
      } else if (row === this.targetRow) {
        return 0.3 / (1 + Math.abs(col - adjustTargetCol));
      } else {
        return null;
      }
    }
  },
  mounted() {
    const vm = this;
    this.timer = setInterval(vm.move, 200);
    document.onkeyup = vm.changeDirection;
    window.addEventListener("keydown", function(e) {
      if (e.key == "ArrowDown" || e.key == "ArrowUp") {
        e.preventDefault();
      }
    });
  },
  created() {
    if (localStorage.getItem("bestScore")) {
      this.bestScore = localStorage.getItem("bestScore");
    }
    this.setTarget();
    this.setInit();
  },
  destroyed() {
    document.onkeyup = null;
  }
};
</script>

<style scoped lang="scss">
.game-page {
  width: 1280px;
  height: 800px;
  color: #fff;
  padding: 80px;
  position: relative;
}
.decorate-text {
  position: absolute;
  font-size: 36px;
  line-height: 43px;
  &:nth-child(1) {
    transform-origin: right top;
    transform: translateX(-100%) rotate(-90deg);
    top: 27px;
    left: 27px;
  }
  &:nth-child(2) {
    transform-origin: right top;
    transform: translateY(100%) rotate(90deg);
    bottom: 27px;
    right: 27px;
  }
  &:nth-child(3) {
    top: 27px;
    left: 64px;
  }
  &:nth-child(4) {
    bottom: 27px;
    right: 64px;
    transform: rotate(180deg);
  }
}
h1 {
  color: #fff;
  font-size: 36px;
  line-height: 43px;
  margin-bottom: 40px;
  position: relative;
  &::before,
  &::after {
    content: "";
    position: absolute;
    width: 40px;
    height: 40px;
    background-color: #fff;
  }
  &::before {
    top: 0;
    left: -60px;
  }
  &::after {
    top: 0;
    right: -60px;
  }
}
p {
  color: #00ffe2;
  font-size: 24px;
  line-height: 29px;
}
.current-score,
.best-score {
  font-size: 16px;
  position: absolute;
  span:nth-child(2) {
    margin-left: 8px;
    font-size: 36px;
    line-height: 43px;
  }
}
.best-score,
.current-score {
  display: flex;
  justify-content: center;
  align-items: center;
}
.current-score {
  top: 27px;
  right: 80px;
}
.best-score {
  left: 80px;
  bottom: 27px;
}

.game-panel {
  display: flex;
  flex-wrap: wrap;
}
.grid {
  width: calc(1120px / 28);
  height: calc(640px / 16);
  background-color: #00035a;
  border: 1px solid #002a9d;
  position: relative;
  .overlay {
    width: 100%;
    height: 100%;
    position: absolute;
    top: 0;
    left: 0;
    background-color: #fff;
  }
}
.target {
  width: 100%;
  height: 100%;
  position: relative;
  img {
    width: 200%;
    height: 200%;
    position: absolute;
    top: -50%;
    left: -50%;
  }
  .overlay {
    width: 100%;
    height: 100%;
    position: absolute;
    top: 0;
    left: 0;
    background-color: #fff;
    opacity: 0.2;
  }
}
.snake {
  position: absolute;
  top: 0;
  width: 100%;
  height: 100%;
  background-color: #fff;
  z-index: 10;
}
</style>

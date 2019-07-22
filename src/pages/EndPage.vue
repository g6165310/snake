<template>
  <div class="end-page">
    <h3>GAME OVER</h3>
    <div class="score-container">
      <div class="score">
        score
        <span>{{score}}</span>
      </div>
      <span>/</span>
      <div class="best-score">
        best
        <span>{{best}}</span>
      </div>
    </div>
    <span class="restart">RESTART?</span>
    <div class="hint">
      <span>Yes[Y]</span>
      <span>NO[N]</span>
    </div>
  </div>
</template>

<script>
export default {
  name: "EndPage",
  data(){
    return{
      best:0
    }
  },
  props:['isEnd','score'],
  methods: {
    isRestart(e){
      if(e.keyCode==89){
        this.$emit('changeStatus','game')
      }
      if(e.keyCode==78){
        this.$emit('changeStatus','start')
      }
    }
  },
  mounted(){
    document.addEventListener('keyup',this.isRestart)
  },
  created(){
    this.best = localStorage.getItem('bestScore')
  }
};
</script>

<style scoped lang="scss">
.end-page {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
h3 {
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
.score-container {
  color: #fff;
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 64px;
  & > span {
    font-size: 36px;
    line-height: 43px;
    margin: 0px 90px;
  }
}
.restart {
  font-size: 24px;
  color: #000;
  background-color: #fff;
  line-height: 29px;
  padding: 8px;
  margin-bottom: 40px;
}
.score,
.best-score {
  display: flex;
  flex-direction: column;
  text-align: center;
  span {
    font-size: 36px;
    line-height: 43px;
  }
}
.hint{
  display: flex;
  justify-content: center;
  align-items: center;
  color: #00FFE2;
  font-size: 24px;
  line-height: 29px;
  span:nth-child(1){
    margin-right: 86px;
  }
}
</style>

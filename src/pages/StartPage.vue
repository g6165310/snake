<template>
  <div class="start-page">
    <h1>SNAAAAAAKE</h1>
    <p>PRESS [SPACE] TO START</p>
  </div>
</template>

<script>
export default {
  name: "StartPage",
  methods: {},
  mounted() {
    const vm = this;
    document.onkeyup = e => {
      e.preventDefault();
      if (e.code === "Space") {
        this.$emit("changeStatus", "game");
      }
    };
    window.addEventListener("keydown", function(e) {
      if (e.code == "Space") {
        e.preventDefault();
      }
    });
  }
};
</script>

<style scoped lang="scss">
.start-page {
  background-image: url("../assets/bg-start.svg");
  background-repeat: no-repeat;
  background-size: cover;
  background-position: center;
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
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
</style>

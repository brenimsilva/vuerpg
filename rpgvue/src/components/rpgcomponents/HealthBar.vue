<template>
  <section>
    <div>
      <h4>Monster Health</h4>
      <div class="outerBar">
        <div class="healthBar"></div>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  props: {
    healthPercentage: Number,
  },
};
</script>

<style scoped>
.outerBar {
  border: 1px solid black;
  width: 200px;
  height: 30px;
}
.healthBar {
  width: v-bind(healthPercentage + "%");
  height: 100%;
  background-color: green;
}
section {
  display: flex;
  justify-content: center;
  text-align: center;
  border-radius: 8px;
  box-shadow: 1px 1px 5px rgba(0, 0, 0, 0.356);
  width: 250px;
}
</style>

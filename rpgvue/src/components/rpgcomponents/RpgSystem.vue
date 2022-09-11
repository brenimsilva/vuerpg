<template>
  <HealthBar v-model:healthPercentage="healthPercentage" />
  <Command @click="attack(10)" />
</template>

<script>
import HealthBar from "./HealthBar.vue";
import Command from "./Command.vue";

export default {
  data() {
    return {
      healthPercentage: 100,
      maxHP: 100,
      HP: 100,
    };
  },
  methods: {
    attack(damage) {
      this.HP -= damage;
      this.healthPercentage = ((this.HP - damage) * 100) / this.maxHP;
      console.log(this.HP);
    },
  },
  components: {
    HealthBar,
    Command,
  },
};
</script>

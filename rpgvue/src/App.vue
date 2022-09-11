<template>
  <header>
    <h1>Monster Slayer</h1>
  </header>
  <div id="game">
    <section id="monster" class="container">
      <h2>Monster Health</h2>
      <div class="healthbar">
        <div class="healthbar__value" :style="monsterBarStyle"></div>
      </div>
    </section>
    <section id="player" class="container">
      <h2>Your Health</h2>
      <div class="healthbar">
        <div :style="playerBarStyle" class="healthbar__value"></div>
      </div>
    </section>
    <section id="controls" v-if="!gameOver">
      <button @click="attackMonster">ATTACK</button>
      <button :disabled="specialAttackDisabled" @click="specialAttackMonster">
        SPECIAL ATTACK
      </button>
      <button @click="playerHeal">HEAL</button>
      <button @click="playerSurrender">SURRENDER</button>
    </section>
    <section id="controls" v-else>
      <h4>Game Over -> {{ winner }} wins!</h4>
      <button @click="restartGame">Restart</button>
    </section>
    <section id="log" class="container">
      <h2>Battle Log</h2>
      <ul>
        <li v-for="log in battleLog">
          <span
            :class="log.actionBy === 'Player' ? 'log--player' : 'log--monster'"
            >{{ log.actionBy }}</span
          >
          <span v-if="log.actionType === 'healed'">
            heals himself for
            <span class="log--heal"
              >{{ log.actionValue }}{{ log.actionStat }}</span
            ></span
          >
          <span v-else>
            attacks and deals
            <span class="log--damage"
              >{{ log.actionValue }}{{ log.actionStat }}</span
            ></span
          >
          <span></span>
          <span></span>
        </li>
      </ul>
    </section>
  </div>
</template>

<script>
export default {
  components: {},
  data() {
    return {
      playerHealth: 100,
      monsterHealth: 100,
      currentRound: 0,
      specialAttack: {
        cooldown: false,
        cooldownCounter: 0,
      },
      gameOver: false,
      winner: null,
      battleLog: [],
    };
  },
  watch: {
    playerHealth(value) {
      if (value <= 0 && this.monsterHealth <= 0) {
        this.winner = "draw";
        this.gameOver = true;
      } else if (value <= 0) {
        this.winner = "Monster";
        this.gameOver = true;
      }
    },
    monsterHealth(value) {
      if (value <= 0 && this.playerHealth <= 0) {
        this.winner = "draw";
        this.gameOver = true;
      } else if (value <= 0) {
        this.winner = "Player";
        this.gameOver = true;
      }
    },
  },
  created() {
    return {};
  },
  computed: {
    monsterBarStyle() {
      if (this.monsterHealth < 0) {
        return { width: "0%" };
      }
      return { width: this.monsterHealth + "%" };
    },
    playerBarStyle() {
      if (this.playerHealth < 0) {
        return { width: "0%" };
      }
      return { width: this.playerHealth + "%" };
    },
    specialAttackDisabled() {
      if (this.specialAttack.cooldownCounter >= 3) {
        this.specialAttack.cooldown = false;
        this.specialAttack.cooldownCounter = 0;
      }
      console.log(this.specialAttack.cooldownCounter);
      const disabled = this.specialAttack.cooldown !== false;
      return disabled;
    },
  },
  methods: {
    attackMonster() {
      if (this.specialAttack.cooldown === true) {
        this.specialAttack.cooldownCounter++;
      }
      const damage = this.getRandomNumber(5, 12);
      this.monsterHealth -= damage;
      this.currentRound++;
      this.generateLog("Player", "dealt", damage, "HP");
      this.attackPlayer();
    },
    attackPlayer() {
      const damage = this.getRandomNumber(8, 15);
      this.playerHealth -= damage;
      this.generateLog("Monster", "dealt", damage, "HP");
    },
    specialAttackMonster() {
      this.specialAttack.cooldown = true;
      this.currentRound++;
      const damage = this.getRandomNumber(10, 25);
      this.monsterHealth -= damage;
      this.generateLog("Player", "dealt", damage, "HP");
      this.attackPlayer();
    },
    playerHeal() {
      this.currentRound++;
      if (this.specialAttack.cooldown === true) {
        this.specialAttack.cooldownCounter++;
      }
      const heal = this.getRandomNumber(12, 24);
      if (this.playerHealth + heal > 100) {
        this.playerHealth = 100;
      } else {
        this.playerHealth += heal;
      }
      this.generateLog("Player", "healed", heal, "HP");
      this.attackPlayer();
    },
    getRandomNumber(min, max) {
      return Math.floor(Math.random() * (max - min)) + min;
    },
    restartGame() {
      this.playerHealth = 100;
      this.monsterHealth = 100;
      this.currentRound = 0;
      this.specialAttack = {
        cooldown: false,
        cooldownCounter: 0,
      };
      this.battleLog = [];
      this.gameOver = false;
      this.winner = null;
    },
    playerSurrender() {
      this.winner = "Monster";
      this.gameOver = true;
    },
    generateLog(who, verb, ammount, stat) {
      const string = {
        actionBy: who,
        actionType: verb,
        actionValue: ammount,
        actionStat: stat,
      };
      this.battleLog.unshift(string);
    },
  },
};
</script>

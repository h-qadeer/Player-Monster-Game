<template>
  <div id="app">
    <section class="row">
      <div class="small-6 columns">
        <h1 class="text-center">YOU</h1>
        <div class="healthbar">
          <div
            class="healthbar text-center"
            style="background-color: green; margin: 0; color: white;"
            :style="{ width: playerHealth + '%' }"
          >
            {{ playerHealth }}
          </div>
        </div>
      </div>
      <div class="small-6 columns">
        <h1 class="text-center">MONSTER</h1>
        <div class="healthbar">
          <div
            class="healthbar text-center"
            style="background-color: green; margin: 0; color: white;"
            :style="{ width: monsterHealth + '%' }"
          >
            {{ monsterHealth }}
          </div>
        </div>
      </div>
    </section>
    <section class="row controls" v-if="!gameIsRunning">
      <div class="small-12 columns">
        <button class="start-game" @click="startGame">START NEW GAME</button>
      </div>
    </section>
    <section class="row controls" v-else>
      <div class="small-12 columns buttons">
        <button class="attack " @click="attack">ATTACK</button>
        <button class="special-attack" @click="specialAttack">
          SPECIAL ATTACK
        </button>
        <button class="heal" @click="heal">HEAL</button>
        <button class="give-up" @click="giveUp">GIVE UP</button>
      </div>
    </section>
    <section class="row log" v-if="turns.length > 0">
      <div class="small-12 columns">
        <ul>
          <li
            v-for="turn in turns"
            :key="turn.text"
            :class="{
              'player-turn': turn.isPlayer,
              'monster-turn': !turn.isPlayer,
              'player-heal': turn.isHeal,
              'player-turn': !turn.isHeal
            }"
          >
            {{ turn.text }}
          </li>
        </ul>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  data() {
    return {
      playerHealth: 100,
      monsterHealth: 100,
      gameIsRunning: false,
      turns: []
    };
  },
  methods: {
    startGame() {
      this.gameIsRunning = true;
      this.playerHealth = 100;
      this.monsterHealth = 100;
      this.turns = [];
    },
    attack() {
      var damage = this.calculateDamage(3, 10);
      this.monsterHealth -= damage;
      this.turns.unshift({
        isPlayer: true,
        // isHeal: false,
        text: "Player hits Monster for " + damage
      });
      if (this.checkWin()) {
        return;
      }

      this.monsterAttack();
    },
    specialAttack() {
      var damage = this.calculateDamage(10, 20);
      this.monsterHealth -= damage;
      this.turns.unshift({
        isPlayer: true,
        // isHeal: false,
        text: "Player hits Monster hard for " + damage
      });
      if (this.checkWin()) {
        return;
      }
      this.monsterAttack();
    },
    heal() {
      if (this.playerHealth <= 90) {
        this.playerHealth += 10;
      } else {
        this.playerHealth = 100;
      }
      this.turns.unshift({
        isPlayer: true,
        isHeal: true,
        text: "Player heals for 10"
      });
      this.monsterAttack();
    },
    giveUp() {
      this.gameIsRunning = false;
    },
    monsterAttack() {
      var damage = this.calculateDamage(5, 12);
      this.playerHealth -= damage;
      this.checkWin();
      this.turns.unshift({
        isPlayer: false,
        isHeal: false,
        text: "Monster hits Player for " + damage
      });
    },
    calculateDamage(min, max) {
      return Math.max(Math.floor(Math.random() * max) + 1, min);
    },
    checkWin() {
      if (this.monsterHealth <= 0) {
        if (confirm("You won! New Game ?")) {
          this.startGame();
        } else {
          this.gameIsRunning = false;
        }
        return true;
      } else if (this.playerHealth <= 0) {
        if (confirm("You lost! New Game ?")) {
          this.startGame();
        } else {
          this.gameIsRunning = false;
        }
        return true;
      }
      return false;
    }
  }
};
</script>

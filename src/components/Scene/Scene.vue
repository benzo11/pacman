<template>
  <div class="pacman__viewport">
    <div class="pacman__header">
      <div>
        <h1>Pacman</h1>
        <h2>Game status: {{ gameStatus }}</h2>
        <h3>Score: {{ points }}</h3>
      </div>
      <div>
        <p>Time start: {{ gameplayTime.start }}</p>
        <p>Time end: {{ gameplayTime.end }}</p>
        <p>Duration: {{ currentDuration }}</p>
      </div>
    </div>
    <div class="pacman__container">
      <Pacman ref="pacman"></Pacman>
      <div class="food__container" v-for="foodItem in food" :key="foodItem.key">
        <Food
          v-bind:position="foodItem.position"
          v-bind:uniqueKey="foodItem.ref"
          v-bind:ref="foodItem.ref"
        >
        </Food>
      </div>
      <Ghost ref="ghost_0" v-bind:ghostType="'gray'"></Ghost>
      <Ghost ref="ghost_1" v-bind:ghostType="'blue'"></Ghost>
      <Ghost ref="ghost_2" v-bind:ghostType="'green'"></Ghost>
      <Ghost ref="ghost_3" v-bind:ghostType="'blue'"></Ghost>
    </div>
    <div class="pacman__modal_game_over" v-bind:class="statusClass">
      <div class="death-background">
        <p>You Died</p>
        <div class="game__stats">
          <p>Total score: {{ points }}</p>
          <p>Time: {{ durationCalc }}</p>
          <button @click="reloadPage">Retry</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Pacman from "../Pacman/Pacman.vue";
import Food from "../Food/Food.vue";
import Ghost from "../Ghost/Ghost.vue";
import moment from "moment";

export default {
  components: { Pacman, Food, Ghost },
  name: "Scene",
  data: function () {
    return {
      food: [],
      foodSize: 50,
      border: 20,
      topScoreBoard: 200,
      amountOfFood: 0,
      points: 0,
      gameStatus: "Start",
      status: "playing",
      gameplayTime: {
        start: new moment(),
        end: new moment(),
      },
      duration: "",
      crashed: false,
      currentDuration: 0,
    };
  },
  mounted: function () {
    this.amountOfFood =
      ((window.innerWidth - this.border - this.foodSize) *
        (window.innerHeight - this.border - this.topScoreBoard)) /
      (this.foodSize * this.foodSize);
    this.prepareFood();
    this.intervalFood = setInterval(this.checkFoodCollision, 100);
    this.intervalCrash = setInterval(this.ghostCrash, 100);
    this.gameplayTime.start = new moment();
    this.intervalTimer = setInterval(this.timer, 0);
  },
  computed: {
    statusClass: function () {
      return {
        gameOver: this.status == "game_over",
      };
    },
    durationCalc: function () {
      var duration = moment.duration(
        this.gameplayTime.start.diff(this.gameplayTime.end)
      );
      var seconds = Math.abs(duration.asSeconds());
      return seconds + "seconds";
    },
  },
  methods: {
    prepareFood: function () {
      var currentTop = 0;
      var currentLeft = 0;
      for (var i = 0; i < this.amountOfFood; i++) {
        if (currentLeft + this.foodSize >= window.innerWidth - this.border) {
          currentTop += this.foodSize;
          currentLeft = 0;
        }

        if (
          currentTop + this.foodSize >=
          window.innerHeight - this.border - this.topScoreBoard
        ) {
          break;
        }

        var position = { left: currentLeft, top: currentTop };
        currentLeft = currentLeft + this.foodSize;
        this.food.push({
          ref: "food" + i,
          position: position,
          key: i,
        });
      }
    },
    checkFoodCollision() {
      var pacman = this.$refs.pacman;
      var pacmanX = pacman.position.left;
      var pacmanY = pacman.position.top;

      var pacmanLastX = pacman.position.left + pacman.pacmanSize / 2;
      var pacmanLastY = pacman.position.top + pacman.pacmanSize / 2;

      for (var i = 0; i <= this.amountOfFood; i++) {
        var currentFood = this.$refs["food" + i];
        if (currentFood && currentFood[0] && "position" in currentFood[0]) {
          var currentFoodX = currentFood[0].position.left;
          var currentFoodY = currentFood[0].position.top;
          var currentFoodLastX =
            currentFood[0].position.left + currentFood[0].foodSize / 2;
          var currentFoodLastY =
            currentFood[0].position.top + currentFood[0].foodSize / 2;

          if (
            (pacmanX >= currentFoodX && pacmanX <= currentFoodLastX) ||
            (pacmanLastX >= currentFoodX && pacmanLastX <= currentFoodLastX)
          ) {
            if (
              (pacmanY >= currentFoodY && pacmanY <= currentFoodLastY) ||
              (pacmanLastY >= currentFoodY && pacmanLastY <= currentFoodLastY)
            ) {
              if (!currentFood[0].hidden) {
                currentFood[0].ate();
                this.increase();
              }
            }
          }
        }

        if (this.crashed) {
          clearInterval(this.intervalFood);
        }
      }
    },
    ghostCrash() {
      var pacman = this.$refs.pacman;
      var pacmanX = pacman.position.left;
      var pacmanY = pacman.position.top;

      var pacmanLastX = pacman.position.left + pacman.pacmanSize;
      var pacmanLastY = pacman.position.top + pacman.pacmanSize;

      for (var i = 0; i <= 3; i++) {
        var currentGhost = this.$refs["ghost_" + i];
        if (currentGhost) {
          var currentGhostX = currentGhost.position.left;
          var currentGhostY = currentGhost.position.top;
          var currentGhostLastX =
            currentGhost.position.left + currentGhost.ghostSize;
          var currentGhostLastY =
            currentGhost.position.top + currentGhost.ghostSize;

          if (
            (pacmanX >= currentGhostX && pacmanX <= currentGhostLastX) ||
            (pacmanLastX >= currentGhostX && pacmanLastX <= currentGhostLastX)
          ) {
            if (
              (pacmanY >= currentGhostY && pacmanY <= currentGhostLastY) ||
              (pacmanLastY >= currentGhostY && pacmanLastY <= currentGhostLastY)
            ) {
              this.crashed = true;
            }
          }

          if (this.crashed) {
            this.gameOver();
            clearInterval(this.intervalCrash);
            break;
          }
        }
      }
    },
    gameOver() {
      this.gameplayTime.end = new moment();
      this.playing = false;
      this.totalPoints = this.points;
      this.gameStatus = "Game over";
      this.status = "game_over";
      clearInterval(this.intervalTimer);
    },
    increase() {
      var currentPoints = this.points + 1;
      this.points = currentPoints;
    },
    timer() {
      var currentTime = new moment();
      var duration = moment.duration(this.gameplayTime.start.diff(currentTime));
      var seconds = Math.abs(duration.asSeconds());
      this.currentDuration = seconds;
    },
    reloadPage() {
      location.reload();
    }
  },
};
</script>
<style scoped>
.pacman__viewport {
  background: black;
  width: 100vw;
  display: flex;
  height: 100vh;
  flex-direction: column;
}

.pacman__container {
  margin: 1rem;
  position: relative;
  height: 100%;
  width: 100%;
}

.food__container {
  position: relative;
}

.pacman__header {
  height: 200px;
  background: gray;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-left: 1rem;
  padding-right: 1rem;
}

.pacman__modal_game_over {
  display: none;
}

.pacman__modal_game_over.gameOver {
  display: block;
  position: absolute;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 9999;
  background: black;
  opacity: 0.9;
}
.death-background {
  background: black;
  height: 200px;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  text-align: center;
  opacity: 1;
  color: #ff0000;
  font-family: OptimusPrinceps, sans-serif;
  letter-spacing: 2px;
  font-weight: 400;
  font-size: 5em;
  animation: fade-in 5s linear, text-zoom 5s linear;
  flex-direction: column;
}

@keyframes fade-in {
  0% {
    opacity: 0;
  }
  25% {
    opacity: 1;
  }
  100% {
    opacity: 1;
  }
}

@keyframes text-zoom {
  0% {
    font-size: 2em;
  }
  25% {
    font-size: 3em;
  }
  50% {
    font-size: 4em;
  }
  75% {
    font-size: 5em;
  }
  100% {
    font-size: 5em;
  }
}
</style>
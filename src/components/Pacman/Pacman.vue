<template>
  <div
    v-bind:style="positionConvert"
    class="figure__pacman"
    v-bind:class="['movement__' + movement]"
  >
    <div class="pacman__eye" v-bind:class="['eye__' + movement]"></div>
    <div class="pacman__mouth"></div>
  </div>
</template>
<script>
export default {
  name: "Pacman",
  mounted: function () {
    setInterval(this.move, 100);
    document.addEventListener("keydown", this.handleKeyDown.bind(this));
  },
  data: function () {
    return {
      movement: "-none-",
      position: { top: 0, left: 0 },
      velocity: 20,
      pacmanSize: 60,
      border: 20,
      topScoreBoard: 200,
    };
  },
  computed: {
    positionConvert: function () {
      return {
        top: this.position.top + "px",
        left: this.position.left + "px",
      };
    },
  },
  methods: {
    handleKeyDown(e) {
      var arrows = [37, 38, 39, 40];

      if (arrows.indexOf(e.keyCode) >= 0) {
        this.rotate(e.keyCode);
      }
    },

    rotate(keypressed) {
      if (keypressed === 37) {
        this.movement = "left";
      } else {
        if (keypressed === 38) {
          this.movement = "up";
        } else {
          if (keypressed === 39) {
            this.movement = "right";
          } else {
            this.movement = "down";
          }
        }
      }
    },
    move() {
      var currentLeft = this.position.left,
        currentTop = this.position.top;
      switch (this.movement) {
        case "right":
          this.position = {
            top: currentTop,
            left: Math.min(
              currentLeft + this.velocity,
              window.innerWidth - this.border - this.pacmanSize
            ),
          };
          break;
        case "left":
          this.position = {
            top: currentTop,
            left: Math.max(currentLeft - this.velocity, 0),
          };
          break;
        case "up":
          this.position = {
            top: Math.max(currentTop - this.velocity, 0),
            left: currentLeft,
          };
          break;
        case "down":
          this.position = {
            top: Math.min(
              currentTop + this.velocity,
              window.innerHeight -
                this.pacmanSize -
                this.border -
                this.topScoreBoard
            ),
            left: currentLeft,
          };
          break;
      }
    },
  },
};
</script>

<style scoped>

.figure__pacman {
  width: 60px;
  height: 60px;
  border-radius: 50%;
  background: #f2d648;
  position: absolute;
  transition: left 120ms, top 120ms;
}

.pacman__eye {
  position: absolute;
  width: 10px;
  height: 10px;
  border-radius: 50%;
  top: 10px;
  right: 28px;
  background: #333333;
}

.pacman__mouth {
  background: #000;
  position: absolute;
  width: 100%;
  height: 100%;
  clip-path: polygon(100% 74%, 44% 48%, 100% 21%);
  animation-name: eat;
  animation-duration: 0.7s;
  animation-iteration-count: infinite;
}

.movement__up {
  transform: rotate(-90deg);
}
.movement__down {
  transform: rotate(90deg);
}
.movement__left {
  transform: rotate(180deg);
}
.movement__right {
  transform: rotate(0);
}

.eye__left {
  top: 40px;
}

@keyframes eat {
  0% {
    clip-path: polygon(100% 74%, 44% 48%, 100% 21%);
  }
  25% {
    clip-path: polygon(100% 60%, 44% 48%, 100% 40%);
  }
  50% {
    clip-path: polygon(100% 50%, 44% 48%, 100% 50%);
  }
  75% {
    clip-path: polygon(100% 59%, 44% 48%, 100% 35%);
  }
  100% {
    clip-path: polygon(100% 74%, 44% 48%, 100% 21%);
  }
}
</style>
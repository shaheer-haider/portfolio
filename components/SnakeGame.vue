<template>
  <div id="console">
    <!-- bolts -->
    <img
      id="corner"
      src="/icons/console/bolt-up-left.svg"
      alt=""
      class="absolute top-2 left-2 opacity-70"
    />
    <img
      id="corner"
      src="/icons/console/bolt-up-right.svg"
      alt=""
      class="absolute top-2 right-2 opacity-70"
    />
    <img
      id="corner"
      src="/icons/console/bolt-down-left.svg"
      alt=""
      class="absolute bottom-2 left-2 opacity-70"
    />
    <img
      id="corner"
      src="/icons/console/bolt-down-right.svg"
      alt=""
      class="absolute bottom-2 right-2 opacity-70"
    />

    <!-- Game Screen -->
    <div class="relative">
      <div id="game-screen" ref="gameScreen"></div>
      <img
        v-if="!isBackgroundAudioMuted"
        @click="
          () => {
            isBackgroundAudioMuted = true;
            backgroundAudio.muted = true;
          }
        "
        src="@/assets/icons/play-music.svg"
        alt=""
        class="absolute top-2 right-2 opacity-70 cursor-pointer"
      />
      <img
        @click="
          () => {
            isBackgroundAudioMuted = false;
            backgroundAudio.muted = false;
          }
        "
        v-else
        src="@/assets/icons/pause-music.svg"
        alt=""
        class="absolute top-2 right-2 opacity-70 cursor-pointer"
      />
    </div>

    <button id="start-button" class="font-fira_retina" @click="startGame">
      press-space
    </button>

    <!-- Game Over -->
    <div id="game-over" class="hidden">
      <span
        class="font-fira_retina text-blue-700 bg-bluefy-dark h-12 flex items-center justify-center"
        >GAME OVER!</span
      >
      <button
        class="font-fira_retina text-menu-text text-sm flex items-center justify-center w-full py-6 hover:text-white"
        @click="startAgain"
      >
        start-again
      </button>
    </div>

    <div id="congrats" class="hidden">
      <span
        class="font-fira_retina text-blue-600 bg-bluefy-dark h-12 flex items-center justify-center"
        >WELL DONE!</span
      >
      <button
        class="font-fira_retina text-menu-text text-sm flex items-center justify-center w-full py-6 hover:text-white"
        @click="startAgain"
      >
        play-again
      </button>
    </div>
    <SnakeGameMenu
      :snakeColor="snakeColor"
      :foodColor="foodColor"
      @move="move"
      @updateSnakeColor="
        (e) => {
          snakeColor = e;
          render();
        }
      "
      @updateFoodColor="
        (e) => {
          foodColor = e;
          render();
        }
      "
    />
  </div>
</template>

<script>
import SnakeGameMenu from "./SnakeGameMenu.vue";

export default {
  data() {
    return {
      foodColor: "#E0FFF8",
      snakeColor: "#E0FFF8",
      backgroundAudio: null,
      isBackgroundAudioMuted: false,
      score: 0,
      gameInterval: null,
      gameStarted: false,
      gameOver: false,
      food: { x: 10, y: 5 },
      snake: [
        { x: 10, y: 12 },
        { x: 10, y: 13 },
        { x: 10, y: 14 },
        { x: 10, y: 15 },
        { x: 10, y: 16 },
        { x: 10, y: 17 },
        { x: 10, y: 18 },
        { x: 11, y: 18 },
        { x: 12, y: 18 },
        { x: 13, y: 18 },
        { x: 14, y: 18 },
        { x: 15, y: 18 },
        { x: 15, y: 19 },
        { x: 15, y: 20 },
        { x: 15, y: 21 },
        { x: 15, y: 22 },
        { x: 15, y: 23 },
        { x: 15, y: 24 },
      ],
      direction: "up",
    };
  },
  methods: {
    startGame() {
      if (this.backgroundAudio.paused) {
        if (this.backgroundAudio.src != "/music/game-background.mp3") {
          this.backgroundAudio.src = "/music/game-background.mp3";
        }
        this.backgroundAudio.currentTime = 0;
        this.backgroundAudio.play();
      }
      document.getElementById("start-button").style.display = "none";
      // start game
      this.gameStarted = true;
      this.gameInterval = setInterval(this.moveSnake, 70);
    },
    startAgain() {
      this.backgroundAudio.pause();
      this.backgroundAudio.src = "/music/game-background.mp3";
      this.backgroundAudio.load();
      this.backgroundAudio.play();

      // Mostrar botón de start-game
      document.getElementById("start-button").style.display = "block";
      // Ocultar game over
      document.getElementById("game-over").style.display = "none";
      document.getElementById("congrats").style.display = "none";
      // reiniciar datos del juego
      this.gameStarted = false;
      this.gameOver = false;
      this.restartScore();
      this.food = {
        x: 10,
        y: 5,
      };
      (this.snake = [
        { x: 10, y: 12 },
        { x: 10, y: 13 },
        { x: 10, y: 14 },
        { x: 10, y: 15 },
        { x: 10, y: 16 },
        { x: 10, y: 17 },
        { x: 10, y: 18 },
        { x: 11, y: 18 },
        { x: 12, y: 18 },
        { x: 13, y: 18 },
        { x: 14, y: 18 },
        { x: 15, y: 18 },
        { x: 15, y: 19 },
        { x: 15, y: 20 },
        { x: 15, y: 21 },
        { x: 15, y: 22 },
        { x: 15, y: 23 },
        { x: 15, y: 24 },
      ]),
        (this.direction = "up");
      // limpiar intervalo de juego
      clearInterval(this.gameInterval);
      this.render();
    },
    // ... resto del código
    moveSnake() {
      let newX = this.snake[0].x;
      let newY = this.snake[0].y;
      switch (this.direction) {
        case "up":
          newY--;
          break;
        case "down":
          newY++;
          break;
        case "left":
          newX--;
          break;
        case "right":
          newX++;
          break;
      }
      // check if snake dont leave from game window
      // and check if snake dont eat itself
      if (
        newX >= 0 &&
        newX < 24 &&
        newY >= 0 &&
        newY < 40 &&
        !this.snake.find((snakeCell) => snakeCell.x === newX && snakeCell.y === newY)
      ) {
        /* snake move next cell */
        this.snake.unshift({ x: newX, y: newY });
        /* check snake next cell is food */
        if (newX === this.food.x && newY === this.food.y) {
          // add score
          this.score++;
          // add food to score board
          const scoreFoods = document.getElementsByClassName("food");
          scoreFoods[this.score - 1].style.opacity = 1;
          // check if score is 10 (max score)
          if (this.score === 10) {
            // move snake head to food (fix snake head position at end)
            this.snake.unshift({ x: newX, y: newY }); // move head
            this.food = { x: null, y: null }; // remove food
            clearInterval(this.gameInterval); // stop game
            document.getElementById("congrats").style.display = "block"; // show congrats
            this.gameOver = true; // game over
            this.gameStarted = false; // stop game
          } else {
            // create new food
            this.food = {
              x: Math.floor(Math.random() * 24),
              y: Math.floor(Math.random() * 40),
            };
          }
        } else {
          // if next cell is not food: snake pop last cell
          this.snake.pop();
        }
      } else {
        // GAME OVER: if snake leave from game window or eat itself
        clearInterval(this.gameInterval);
        document.getElementById("game-over").style.display = "block";
        this.gameStarted = false;
        this.gameOver = true;
        this.backgroundAudio.pause();
        this.backgroundAudio.src = "/music/game-over.mp3";
        this.backgroundAudio.load();
        this.backgroundAudio.play();
      }
      this.render();
    },
    render() {
      let gameScreen = this.$refs.gameScreen;
      gameScreen.innerHTML = "";
      // responsive cell screen
      // (this.$refs.gameScreen.offsetWidth / 20) + "px";
      /* const widthCells = window.innerWidth > 1536 ? 24 : 20; */
      const cellSize = window.innerWidth > 1536 ? "10px" : "8px";
      // eje y
      for (let i = 0; i < 40; i++) {
        // exe x
        for (let j = 0; j < 24; j++) {
          /* cell style */
          let cell = document.createElement("div");
          cell.classList.add("cell");
          cell.style.width = cellSize;
          cell.style.height = cellSize;
          cell.style.display = "flex";
          cell.style.flexShrink = 0;
          cell.classList.add("black");
          /* Food cell style */
          if (j === this.food.x && i === this.food.y) {
            cell.style.backgroundColor = this.foodColor;
            cell.style.borderRadius = "50%";
            cell.style.boxShadow = `0 0 10px ${this.foodColor}`;
          }
          /* Estilo de la serpiente a medida que va crediendo */
          let snakeCell = this.snake.find(
            (snakeCell) => snakeCell.x === j && snakeCell.y === i
          );
          if (snakeCell) {
            cell.style.backgroundColor = this.snakeColor;
            cell.style.opacity = 1 - this.snake.indexOf(snakeCell) / this.snake.length;
            cell.classList.add("green");
          }
          /* Estilo de la cabeza */
          if (snakeCell && this.snake.indexOf(snakeCell) === 0) {
            let headRadius = "5px";
            if (this.direction === "up") {
              cell.style.borderTopLeftRadius = headRadius;
              cell.style.borderTopRightRadius = headRadius;
            }
            if (this.direction === "down") {
              cell.style.borderBottomLeftRadius = headRadius;
              cell.style.borderBottomRightRadius = headRadius;
            }
            if (this.direction === "left") {
              cell.style.borderTopLeftRadius = headRadius;
              cell.style.borderBottomLeftRadius = headRadius;
            }
            if (this.direction === "right") {
              cell.style.borderTopRightRadius = headRadius;
              cell.style.borderBottomRightRadius = headRadius;
            }
          }
          gameScreen.appendChild(cell);
        }
      }
    },
    restartScore() {
      this.score = 0;
      const scoreFoods = document.getElementsByClassName("food");
      for (let i = 0; i < scoreFoods.length; i++) {
        scoreFoods[i].style.opacity = 0.3;
      }
    },
    move(direction) {
      switch (direction) {
        case "up":
          if (this.direction !== "down") {
            this.direction = "up";
          }
          break;
        case "down":
          if (this.direction !== "up") {
            this.direction = "down";
          }
          break;
        case "left":
          if (this.direction !== "right") {
            this.direction = "left";
          }
          break;
        case "right":
          if (this.direction !== "left") {
            this.direction = "right";
          }
          break;
      }
    },
  },
  mounted() {
    if (!this.backgroundAudio) {
      this.backgroundAudio = new Audio("/music/game-background.mp3");
    }
    document.addEventListener("keydown", (event) => {
      if (this.gameStarted) {
        switch (event.keyCode) {
          case 37:
            if (this.direction !== "right") {
              this.direction = "left";
            }
            break;
          case 38:
            if (this.direction !== "down") {
              this.direction = "up";
            }
            break;
          case 39:
            if (this.direction !== "left") {
              this.direction = "right";
            }
            break;
          case 40:
            if (this.direction !== "up") {
              this.direction = "down";
            }
            break;
        }
      } else {
        switch (event.keyCode) {
          case 32:
            if (this.gameOver) {
              this.startAgain();
            } else {
              this.startGame();
            }
            break;
        }
      }
    });
    /* window.innerWidth < 1536 ? cellSize = 8 : cellSize = 10; */
    /* this.food = {
          x: Math.floor(Math.random() * 24),
          y: Math.floor(Math.random() * 40)
        }; */
    window.onresize = () => {
      this.render();
    };
    this.render();
  },
  unmounted() {
    if (this.backgroundAudio) {
      this.backgroundAudio.pause();
    }
  },
  components: { SnakeGameMenu },
};
</script>

<style>
#console {
  width: 530px;
  height: 475px;
  border: 1px solid black;
  display: flex;
  align-items: center;
  justify-content: space-between;
  background: linear-gradient(to bottom, #c36ffb21, #7000da);
  color: white;
  border-radius: 10px;
  padding: 30px;
  position: relative;
}

#game-screen {
  width: 240px;
  height: 400px;
  border-radius: 10px;
  background-color: rgba(0, 0, 0, 0.84);
  display: flex;
  flex-wrap: wrap;
  box-shadow: inset 0 0 10px #00000071;
}

#start-button {
  padding-inline: 16px;
  padding-block: 8px;
  border-radius: 10px;
  border: 1px solid white;
  background-color: #8254ff;
  color: black;
  cursor: pointer;
  position: absolute;
  bottom: 20%;
  left: 17%;
  font-size: 0.875rem; /* 14px */
  line-height: 1.25rem; /* 20px */
}

#start-button:hover {
  background-color: #6832ff;
}

#console-menu {
  height: 400px;
}

#console-button {
  background-color: #010c15;
  border-radius: 10px;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 50px;
  height: 30px;
}

#console-button:hover {
  background-color: #010c15d8;
  box-shadow: #8400ff 0 0 10px;
}

#instructions {
  background-color: rgba(1, 20, 35, 0.19);
  border-radius: 7px;
  padding: 10px;
}

.food {
  background-color: #000;
  border-radius: 50%;
  box-shadow: 0 0 10px #000;
  width: 8px;
  height: 8px;
  opacity: 0.3;
}

#game-over,
#congrats {
  position: absolute;
  bottom: 12%;
  color: #fb6f92;
  width: 240px;
}

#game-over,
#congrats > span {
  font-size: 1.5rem; /* 24px */
  line-height: 2rem; /* 32px */
}

#corner {
  width: 24px;
  height: 24px;
}

#skip-btn {
  font-size: 14px;
  color: white;
  padding-inline: 16px;
  padding-block: 8px;
  border: 2px solid #000;
  border-radius: 0.5rem; /* 8px */
}
#skip-btn:hover {
  background-color: #6102b9;
}

@media (min-width: 1024px) and (max-width: 1536px) {
  #game-screen {
    width: 192px;
    height: 320px;
  }

  #console {
    width: 420px;
    height: 370px;
    padding: 24px;
  }

  #start-button {
    padding-inline: 12px;
    padding-block: 6px;
    border-radius: 8px;
    bottom: 20%;
    left: 17%;
    font-size: 0.75rem; /* 14px */
    line-height: 1rem; /* 20px */
  }

  #console-menu {
    height: 320px;
  }

  #instructions {
    font-size: 12px;
  }

  #console-button {
    width: 40px;
    height: 25px;
    border-radius: 6px;
  }

  #score-board {
    font-size: 12px;
  }

  .food {
    width: 6px;
    height: 6px;
  }

  #game-over,
  #congrats {
    position: absolute;
    bottom: 10%;
    color: #fb6f92;
    width: 192px;
  }

  #game-over,
  #congrats > span {
    font-size: 1.125rem; /* 18px */
    line-height: 1.75rem; /* 28px */
  }

  #corner {
    width: 20px;
    height: 20px;
  }

  #skip-btn {
    font-size: 12px;
    padding-inline: 12px;
    padding-block: 6px;
    border: 2px solid white;
    border-radius: 0.5rem; /* 8px */
  }
}
</style>

<template>
  <div id="board">
    <div class="board-container">
      <div/>
      <div class="board-title-area">
        <div/>
        <div class="board-position">
          <div v-bind:class="['board-circle', 'top-circle', circleClass()]">GO</div>
        </div>
        <div id="board-info">CONNECT 4</div>
      </div>
      <div class="board-game-area">
        <div/>
        <div v-for="m in 7" :key="m" class="board-column" v-on:click="() => colClick(m-1)">
          <div class="board-column-container">
            <div v-for="n in 6" :key="n" class="board-position">
              <div v-bind:class="['board-circle', playerColor(game.board[m-1][n-1])]"></div>
            </div>
          </div>
        </div>
        <div/>
      </div>
    </div>
    <div v-if="!game.gameStarted" class="board-message-container" v-on:click="startGame">
      <div class="board-message">START</div>
    </div>
    <div v-if="game.gameOver" class="board-message-container" v-on:click="restartGame">
      <div class="board-message">
        {{this.game.player1 ? (this.game.playerWin === 0 ? "NOBODY" : "RED") : (this.game.playerWin === 0 ? "NOBODY" : "YELLOW") }}
        <br>WINS
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Board",
  props: {
    msg: String
  },
  data() {
    return {
      game: this.makeBoard()
    };
  },
  methods: {
    makeBoard() {
      let board = new Array(7);
      for (let i = 0; i < board.length; i++) {
        board[i] = [];
        for (let j = 0; j < 6; j++) {
          board[i].push(0);
        }
      }
      return {
        board,
        gameStarted: false,
        playerSelected: false,
        player1: true,
        count: 0,
        gameOver: false,
        playerWin: 0
      };
    },
    startGame() {
      this.game.gameStarted = true;
    },
    restartGame() {
      this.game = this.makeBoard();
      this.game.gameStarted = true;
    },
    colClick(n) {
      //console.log(n);
      let temp = [...this.game.board];

      if (this.game.gameStarted && !this.game.gameOver) {
        for (let i = temp[n].length - 1; i >= 0; i--) {
          if (temp[n][i] === 0) {
            temp[n][i] = this.game.player1 ? 1 : 5;
            this.$set(this.game, "board", temp);
            this.game.count++;
            if (this.checkWin()) {
              this.game.gameOver = true;
              this.game.playerWin = this.game.player1 ? 1 : 2;
            } else {
              if (this.game.count === 42) {
                this.game.gameOver = true;
              }
              this.game.player1 = !this.game.player1;
            }
            break;
          }
        }
        this.checkWin();
      }
    },
    checkWin() {
      let b = this.game.board;
      let t;
      for (let i = 0; i < 4; i++) {
        for (let j = 5; j >= 0; j--) {
          t = b[i][j] + b[i + 1][j] + b[i + 2][j] + b[i + 3][j];
          if (t === 4 || t === 20) return true;
        }
      }
      for (let j = 0; j < 3; j++) {
        for (let i = 0; i < 7; i++) {
          t = b[i][j] + b[i][j + 1] + b[i][j + 2] + b[i][j + 3];
          if (t === 4 || t === 20) return true;
        }
      }
      for (let j = 5; j > 1; j--) {
        for (let i = 0; i < 4; i++) {
          t = b[i][j] + b[i + 1][j - 1] + b[i + 2][j - 2] + b[i + 3][j - 3];
          if (t === 4 || t === 20) return true;
          t = b[6 - i][j] + b[5 - i][j - 1] + b[4 - i][j - 2] + b[3 - i][j - 3];
          if (t === 4 || t === 20) return true;
        }
      }
      return false;
    },
    playerColor(n) {
      if (n === 0) return "";
      return n === 1 ? "player1Color" : "player2Color";
    },
    circleClass() {
      if (this.game.player1) {
        return " player1Color";
      } else {
        return " player2Color";
      }
    }
  },
  mounted() {
    // fit game to fill viewport
    function handleResize() {
      let game = document.getElementById("board").style;
      let info = document.getElementById("board-info").style;
      let [top] = document.getElementsByClassName("top-circle");
      let msgs = document.getElementsByClassName("board-message");
      let units = window.innerWidth > window.innerHeight ? "vh" : "vw";
      info.fontSize = "10" + units;
      game.height = game.width = "96" + units;
      game.fontSize = "10.34" + units;
      top.style.fontSize = "6" + units;
      top.style.lineHeight = "10" + units;
      for (let msg of msgs) msg.style.fontSize = "20" + units;
    }
    handleResize();
    window.addEventListener("resize", handleResize);
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#board {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  height: 90vw;
  width: 90vw;
  font-family: sans-serif;
  text-align: center;
  box-sizing: border-box;
  user-select: none;
  --boardColor: rgb(26, 81, 182);
  --player1Color: rgb(255, 68, 68);
  --player2Color: rgb(235, 225, 90);
}

.board-message-container {
  display: flex;
  flex-wrap: wrap;
  position: absolute;
  top: 0;
  left: 0;
  background-color: rgba(0, 0, 0, 0.705);
  height: 100%;
  width: 100%;
}

.board-message {
  display: flex;
  color: white;
  justify-content: center;
  align-items: center;
  width: 100%;
  font-size: 20vw;
  font-weight: bold;
  text-shadow: 2px 2px 8px black;
  -webkit-text-stroke-width: 1px;
  -webkit-text-stroke-color: black;
  cursor: pointer;
}

.board-container {
  display: grid;
  grid-template-rows: 2fr 10fr 60fr 2fr;
  width: 100%;
  height: 100%;
  box-shadow: rgba(0, 0, 0, 0.5) 0px 5px 15px;
  background-color: var(--boardColor);
  border-radius: 2vw;
}

.board-title-area {
  display: grid;
  grid-template-columns: 2fr 10fr 60fr 2fr;
}

#board-info {
  font-size: 10vw;
  font-weight: bold;
  color: white;
  text-shadow: 0px 0px 8px rgba(0, 0, 0, 0.94);
  -webkit-text-stroke-width: 1px;
  -webkit-text-stroke-color: black;
}

.board-game-area {
  display: grid;
  grid-template-columns: 2fr repeat(7, 10fr) 2fr;
}

.board-column {
  width: 100%;
  height: 100%;
}

.board-column-container {
  display: grid;
  grid-template-rows: repeat(6, 1fr);
  height: 100%;
  width: 100%;
  border-radius: 15vw;
}

.board-column-container:hover {
  cursor: pointer;
  background-image: linear-gradient(
    rgb(26, 81, 182),
    rgb(0, 36, 104),
    rgb(26, 81, 182)
  );
}

.board-position {
  height: 100%;
  width: 100%;
}

.board-circle {
  font-size: 6vw;
  height: 82%;
  width: 82%;
  margin: 9%;
  background-color: white;
  border-radius: 200px;
  box-shadow: rgba(0, 0, 0, 0.6) 0px 2px 4px inset;
}

.top-circle {
  display: flex;
  align-items: flex-end;
  justify-content: center;
  font-family: "Courier New", Courier, monospace;
  font-size: 6vw;
  font-weight: bold;
  line-height: 10vw;
  overflow: hidden;
  background-color: rgb(107, 168, 107);
  cursor: pointer;
  box-shadow: rgba(0, 0, 0, 1) 0px 2px 2px;
}

.game-start {
  font-size: 3vw;
}

.player1Color {
  background-color: var(--player1Color);
}

.player2Color {
  background-color: var(--player2Color);
}
</style>

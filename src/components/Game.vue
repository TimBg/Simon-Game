<template>
  <div class="wrapper">
    <h1>Simon Says</h1>
    <div class="game-board">
      <div class="simon">
        <ul>
          <li
            class="tile red"
            v-bind:style="{ background: red }"
            @click="clickOnRedArea"
          ></li>
          <li
            class="tile blue"
            v-bind:style="{ background: blue }"
            @click="clickOnBlueArea"
          ></li>
          <li
            class="tile yellow"
            v-bind:style="{ background: yellow }"
            @click="clickOnYellowArea"
          ></li>
          <li
            class="tile green"
            v-bind:style="{ background: green }"
            @click="clickOnGreenArea"
          ></li>
        </ul>
      </div>
    </div>
    <div class="game-info">
      <h2>Раунд: {{ round }}</h2>
      <button @click="start">Старт</button>
    </div>
    <div class="game-options">
      <h2>Опции:</h2>
      <input type="radio" v-model="level" value="1500" checked />Легкий<br />
      <input type="radio" v-model="level" value="1000" />Средний<br />
      <input type="radio" v-model="level" value="400" />Сложный<br />
    </div>
    <div data-action="sound"></div>
  </div>
</template>

<script>
export default {
  name: "Game",
  data: () => ({
    gameSteps: [],
    userSteps: [],
    level: 1500,
    round: undefined,
    isWin: undefined,
    on: true,
    isSuccessStep: undefined,
    idOfInterval: undefined,
    isStrict: true,
    isUpdateOneOfColors: undefined,
    numOfStep: undefined,
    sound: true,
    flash: undefined,
    red: "darkred",
    blue: "darkblue",
    green: "darkgreen",
    yellow: "goldenrod",
    redSound: new Audio("/sounds/1.mp3"),
    blueSound: new Audio("/sounds/2.mp3"),
    greenSound: new Audio("/sounds/3.mp3"),
    yellowSound: new Audio("/sounds/4.mp3"),
  }),
  methods: {
    start() {
      (this.on || this.isWin) && this.play();
    },
    play() {
      this.gameSteps = [];
      this.userSteps = [];
      this.isWin = false;
      this.isSuccessStep = true;
      this.idOfInterval = 0;
      this.on = true;
      this.flash = 0;
      this.numOfStep = 1;
      this.round = 1;
      for (let i = 0; i < 20; i++) {
        this.gameSteps.push(Math.floor(Math.random() * 4) + 1);
      }
      this.isUpdateOneOfColors = true;
      this.idOfInterval = setInterval(this.goToNextArea, this.level);
    },
    backToDefaultColors() {
      this.red = "darkred";
      this.blue = "darkblue";
      this.green = "darkgreen";
      this.yellow = "goldenrod";
    },
    goToNextArea() {
      this.on = false;
      if (this.flash === this.numOfStep) {
        clearInterval(this.idOfInterval);
        this.isUpdateOneOfColors = false;
        this.backToDefaultColors();
        this.on = true;
      }
      if (this.isUpdateOneOfColors) {
        this.backToDefaultColors();
        setTimeout(() => {
          if (this.gameSteps[this.flash] === 1) this.updateBlue();
          if (this.gameSteps[this.flash] === 2) this.updateRed();
          if (this.gameSteps[this.flash] === 3) this.updateYellow();
          if (this.gameSteps[this.flash] === 4) this.updateGreen();
          this.flash++;
        }, 200);
      }
    },
    updateRed() {
      if (this.sound) this.redSound.play();
      this.sound = true;
      this.red = "tomato";
    },
    updateBlue() {
      if (this.sound) this.blueSound.play();
      this.sound = true;
      this.blue = "lightskyblue";
    },
    updateGreen() {
      if (this.sound) this.greenSound.play();
      this.sound = true;
      this.green = "lightgreen";
    },
    updateYellow() {
      if (this.sound) this.yellowSound.play();
      this.sound = true;
      this.yellow = "yellow";
    },
    winGame() {
      this.updateAllColors();
      this.round = "Победа";
      this.isWin = true;
    },
    updateAllColors() {
      this.red = "tomato";
      this.blue = "lightskyblue";
      this.green = "lightgreen";
      this.yellow = "yellow";
    },
    checkСorrectness() {
      if (
        this.userSteps[this.userSteps.length - 1] !==
        this.gameSteps[this.userSteps.length - 1]
      ) {
        this.isSuccessStep = false;
      }
      if (this.userSteps.length === 20 && this.isSuccessStep) this.winGame();
      if (!this.isSuccessStep) {
        this.updateAllColors();
        this.round = "Поражение";
        setTimeout(() => {
          this.round = this.numOfStep;
          this.backToDefaultColors();
          if (this.isStrict) {
            this.play();
          } else {
            this.isUpdateOneOfColors = true;
            this.flash = 0;
            this.userSteps = [];
            this.isSuccessStep = true;
            this.idOfInterval = setInterval(this.goToNextArea, this.level);
          }
        }, 800);
        this.sound = false;
      }
      if (
        this.numOfStep === this.userSteps.length &&
        this.isSuccessStep &&
        !this.isWin
      ) {
        this.numOfStep++;
        this.userSteps = [];
        this.isUpdateOneOfColors = true;
        this.flash = 0;
        this.round = this.numOfStep;
        this.idOfInterval = setInterval(this.goToNextArea, this.level);
      }
    },
    clickOnRedArea() {
      if (this.on) {
        this.userSteps.push(2);
        this.checkСorrectness();
        this.updateRed();
        if (!this.isWin) {
          setTimeout(() => {
            this.backToDefaultColors();
          }, 50);
        }
      }
    },
    clickOnBlueArea() {
      if (this.on) {
        this.userSteps.push(1);
        this.checkСorrectness();
        this.updateBlue();
        if (!this.isWin) {
          setTimeout(() => {
            this.backToDefaultColors();
          }, 50);
        }
      }
    },
    clickOnGreenArea() {
      if (this.on) {
        this.userSteps.push(4);
        this.checkСorrectness();
        this.updateGreen();
        if (!this.isWin) {
          setTimeout(() => {
            this.backToDefaultColors();
          }, 50);
        }
      }
    },
    clickOnYellowArea() {
      if (this.on) {
        this.userSteps.push(3);
        this.checkСorrectness();
        this.updateYellow();
        if (!this.isWin) {
          setTimeout(() => {
            this.backToDefaultColors();
          }, 50);
        }
      }
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
body {
  font-family: Arial, serif;
  color: black;
  -webkit-user-select: none; /* Chrome/Safari */
  -moz-user-select: none; /* Firefox */
  -ms-user-select: none; /* IE10+ */
  -o-user-select: none;
  user-select: none;
}
h1 {
  margin: 1em 0 2em;
  text-align: center;
}
ul {
  list-style: none;
}
ul,
li {
  padding: 0;
  margin: 0;
}
p[data-action="lose"] {
  display: none;
}
.active {
  opacity: 1 !important;
}
.clearfix {
  width: 100%;
  clear: both;
}
.wrapper {
  width: 540px;
  margin: 0 auto;
}
.container {
  width: 305px;
}
.simon {
  background: #fff;
  position: relative;
  float: left;
  margin-right: 3em;
  width: 302px;
  height: 295px;
  -webkit-border-radius: 150px 150px 150px 150px;
  border-radius: 150px 150px 150px 150px;
  -moz-box-shadow: 2px 1px 12px #aaa;
  -webkit-box-shadow: 2px 1px 12px #aaa;
  -o-box-shadow: 2px 1px 12px #aaa;
  box-shadow: 2px 1px 12px #aaa;
}
.tile {
  opacity: 0.6;
  -webkit-transition: opacity 250ms ease;
  -moz-transition: opacity 250ms ease;
  -ms-transition: opacity 250ms ease;
  -o-transition: opacity 250ms ease;
  transition: opacity 250ms ease;
}
.tile.lit {
  opacity: 1;
}
.red,
.blue,
.yellow,
.green {
  height: 290px;
  -webkit-border-radius: 150px 150px 150px 150px;
  border-radius: 150px 150px 150px 150px;
  position: absolute;
  text-indent: 10000px;
}
.red:hover,
.blue:hover,
.yellow:hover,
.green:hover {
  border: 2px solid black;
}
.red {
  clip: rect(0px, 300px, 150px, 150px);
  width: 296px;
}
.blue {
  clip: rect(0px, 150px, 150px, 0px);
  width: 300px;
}
.yellow {
  clip: rect(150px, 150px, 300px, 0px);
  width: 300px;
}
.green {
  clip: rect(150px, 300px, 300px, 150px);
  width: 296px;
}
.game-info {
  margin-top: 90px;
}
.game-info button {
  width: 5em;
  box-sizing: border-box;
  font-size: 1.4em;
  -webkit-border-radius: 10px 10px 10px 10px;
  border-radius: 10px 10px 10px 10px;
  background: #6dabe8;
  border: none;
  padding: 0.3em 0.6em;
}
.game-info button:hover {
  background: #78bcff;
}
.game-options h2 {
  margin-top: 30px;
  margin-bottom: 0;
}
.game-options input[type="radio"] {
  margin-right: 10px;
}
.hoverable:hover {
  cursor: pointer;
}
footer {
  position: absolute;
  bottom: 20px;
  width: 540px;
  clear: both;
  text-align: center;
}
</style>
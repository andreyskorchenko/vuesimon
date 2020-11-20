<template>
  <div class="simon">
    <div class="game">
      <div class="game__circle b50radius">
        <div class="game__red transition" v-bind:class="{'game__red_play': activeRed}" v-on:click="click" data-id="1"></div>
        <div class="game__blue transition" v-bind:class="{'game__blue_play': activeBlue}" v-on:click="click" data-id="2"></div>
        <div class="game__yellow transition" v-bind:class="{'game__yellow_play': activeYellow}" v-on:click="click" data-id="3"></div>
        <div class="game__green transition" v-bind:class="{'game__green_play': activeGreen}" v-on:click="click" data-id="4"></div>
      </div>
    </div>
    
    <div class="info">
      <div class="info__game">
        <h1>Level: {{ level }}</h1>
        <button class="info__start b5radius transition" v-on:click="start">Start</button>
      </div>

      <div class="info__options">
        <h2>Complexity</h2>

        <label class="transition"><input type="radio" v-model="delay" value="1500" name="info__mode" checked>Light</label>
        <label class="transition"><input type="radio" v-model="delay" value="1000" name="info__mode">Middle</label>
        <label class="transition"><input type="radio" v-model="delay" value="400" name="info__mode">Complicated</label>
      </div>
    </div>

    <div
      class="gameover b5radius transition"
      v-bind:class="{'gameover_show': showErrMessage}"
    ><p>{{ errMessage }}</p></div>
    <audio autoplay v-bind:src="sound" type="audio/mp3"></audio>
  </div>
</template>

<script>
import audio1 from '../assets/sounds/1.mp3';
import audio2 from '../assets/sounds/2.mp3';
import audio3 from '../assets/sounds/3.mp3';
import audio4 from '../assets/sounds/4.mp3';

export default {
    name: 'Simon',
    data: () => ({
      level: 0,
      sound: '',
      sequence: [],
      replace: [],
      delay: 1500,
      countClick: 0,
      activeRed: false,
      activeBlue: false,
      activeYellow: false,
      activeGreen: false,
      showErrMessage: false,
      errMessage: ''
    }),
    watch: {
      delay() {
        this.reset();
      }
    },
    methods: {
      start() {
        const random = Math.floor(Math.random() * 4) + 1;
        
        this.reset();
        this.level++;
        this.sequence.push(random);
        this.play(random);
      },
      next() {
        this.level++;
        this.sequence = [];
        this.replace = [];
        this.countClick = 0;

        let countInterval = 0;
        const idInterval = setInterval(() => {
          if (this.level) {
            countInterval++;

            const random = Math.floor(Math.random() * 4) + 1;
            this.sequence.push(random);
            this.play(random);

            if (countInterval === this.level) {
              clearInterval(idInterval);
            }
          } else {
            clearInterval(idInterval);
          }
        }, this.delay);
      },
      play(id) {
        switch (id) {
          case 1:
            this.sound = audio1;
            this.activeRed = true;

            setTimeout(() => {
              this.sound = '';
              this.activeRed = false;
            }, 300);
          break;
          case 2:
            this.sound = audio2;
            this.activeBlue = true;
            
            setTimeout(() => {
              this.sound = '';
              this.activeBlue = false;
            }, 300);
          break;
          case 3:
            this.sound = audio3;
            this.activeYellow = true;
            
            setTimeout(() => {
              this.sound = '';
              this.activeYellow = false;
            }, 300);
          break;
          case 4:
            this.sound = audio4;
            this.activeGreen = true;
            
            setTimeout(() => {
              this.sound = '';
              this.activeGreen = false;
            }, 300);
          break;
        }
      },
      click({ target: el }) {
        if (this.level) {
          const currentID = +el.dataset.id;
          
          this.replace.push(currentID);
          this.play(currentID);
          this.countClick++;

          if (this.countClick === this.level) {
            if (this.sequence.join('') === this.replace.join('')) {
              this.next();
            } else {
              this.gameOver();
            }
          } else {
            if (this.sequence[this.countClick - 1] !== currentID) {
              this.gameOver();
            }
          }
        }
      },
      reset() {
        this.level = 0;
        this.sound = '';
        this.sequence = [];
        this.replace = [];
        this.countClick = 0;
      },
      gameOver() {
        ((level) => {
          this.reset();
          this.errMessage = `Игра окончена! Ваш счет: ${level - 1}`;
          this.showErrMessage = true;
          setTimeout(() => this.showErrMessage = false, 5000);
        })(this.level);
      }
    }
};
</script>

<style lang="scss" scoped>
$font:Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;

.simon {
  width:100%;
  height:100vh;
  display:flex;
  overflow:hidden;
}

.game,
.info {
  height:100vh;
  display:flex;
  flex-direction:column;
  justify-content:center;
  overflow:hidden;
}

.game {
  width:60%;
  align-items:center;
}

.info {
  width:40%;
}

/* ----------------------------------------
  Game
---------------------------------------- */
.game__circle {
  width:360px;
  height:360px;
  background:#FFF;
  box-shadow:0 0 15px 1px rgba(0,0,0, 0.3);
  overflow:hidden;
  cursor:pointer;
  position:relative;
}

.game__red,
.game__blue,
.game__yellow,
.game__green {
  width:180px;
  height:180px;
  position:absolute;

  &:hover {
    width:182px;
    height:182px;
    box-shadow:0 0 15px 1px rgba(0,0,0, 0.3);
    z-index:1;
  }
}

.game__red { background:#E74C3C; }
.game__blue { background:#3498DB; right:0; }
.game__yellow { background:#F1C40F; right:0; bottom:0; }
.game__green { background:#2ECC71; bottom:0; }

.game__red_play,
.game__blue_play,
.game__yellow_play,
.game__green_play {
  width:182px;
  height:182px;
  box-shadow:0 0 15px 1px rgba(0,0,0, 0.3);
  z-index:1;
}

.game__red_play { background:#D83D2D; }
.game__blue_play { background:#2589CC; }
.game__yellow_play { background:#E2B500; }
.game__green_play {background:#1FBD62; }

/* ----------------------------------------
  Info
---------------------------------------- */
.info__game {
  width:100%;
  display:flex;
  flex-direction:column;

  & h1 {
    padding:10px 20px;
    font-size:20px;
    font-family:$font;
  }

  & button {
    width:100px;
    height:40px;
    background:#f1c40f;
    box-shadow:0 0 10px 1px rgba(0,0,0, 0.1);
    cursor:pointer;
    margin:10px 20px;
    color:#FFF;
    font-size:14px;
    font-family:$font;
    
    &:hover {
      background:#f8c90a;
    }

    &:active {
      box-shadow:none;
    }
  }
}

.info__options {
  width:100%;
  display:flex;
  flex-direction:column;
  margin-top:20px;

  & h2 {
    padding:10px 20px;
    font-size:20px;
    font-family:$font;
  }

  & label {
    width:220px;
    cursor:pointer;
    padding:10px;
    font-size:14px;
    font-family:$font;
  }

  & label:hover { background:#F0F0F0; }
  & label input { margin:0 10px; }
}

/* ----------------------------------------
    Game over
---------------------------------------- */
.gameover {
  width:auto;
  height:50px;
  background:rgb(255, 118, 118);
  overflow:hidden;
  padding:0 20px;
  transform:translate(-50%, calc(100% + 10px));
  visibility:hidden;
  opacity:0;
  position:fixed;
  bottom:10px;
  left:50%;

  & p {
    line-height:50px;
    color:#FFF;
    font-size:14px;
    font-family:$font;
  }

  &_show {
    transform:translate(-50%, 0);
    visibility:visible;
    opacity:1;
  }
}
</style>
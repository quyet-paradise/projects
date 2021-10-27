<template>
  <div id="app">
    <body>
        <div class="wrapper clearfix">
          <players 
            v-bind:isWinner="isWinner"
            v-bind:activePlayer="activePlayer"
            v-bind:currentScore="currentScore"
            v-bind:scoresPlayer="scoresPlayer"
          />
          <control 
            v-bind:finalScore="finalScore"
            v-bind:isPlaying="isPlaying"
            v-on:handleChangeFinalScore="handleChangeFinalScore"
            v-on:handleNewGame="handleNewGame"
            v-on:handleRollDice="handleRollDice"
            v-on:handleHoldScore="handleHoldScore"/>
          <dices 
            v-bind:dices="dices"/>

          <popup-rule 
            v-on:handleConfirm="handleConfirm"
            v-bind:isOpenPopup="isOpenPopup" />

        </div>

    </body>
  </div>
</template>

<script>
import Players from './components/Players';
import Control from './components/Control';
import Dices from './components/Dices';
import PopupRule from './components/PopupRule';

export default {
  name: 'app',
  data () {
    return {
      isPlaying: false,
      isOpenPopup: false,
      activePlayer: 1,
      scoresPlayer: [20, 30],
      currentScore: 30,
      dices: [2, 6],
      finalScore: 10
    }
  },
  components: {
    Players,
    Control,
    Dices,
    PopupRule
  }, 
  methods: {
    nextPlayer(e) {
      this.activePlayer = this.activePlayer === 0 ? 1 : 0;
      this.currentScore = 0;
    },

    handleConfirm(e) {
      console.log('handleConfirm App.vue');
      this.isPlaying = true;
      this.isOpenPopup = false;
      this.activePlayer = 0;
      this.scoresPlayer = [0, 0];
      this.currentScore = 0;
      this.dices = [1, 1]
    },
    handleNewGame(e) {
      console.log('handleNewGame App.vue');
      this.isOpenPopup = true;
    },

    handleRollDice(e) {
      console.log('handleRollDice App.vue');
      if(this.isPlaying) {
        // Xoay xuc xac
        var dice1 =Math.floor(Math.random() * 6) + 1;
        var dice2 =Math.floor(Math.random() * 6) + 1;
        console.log(dice1, dice2);
        this.dices = [dice1, dice2];

        if(dice1 === 1 || dice2 === 1) {
          let activePlayer = this.activePlayer;
          setTimeout(() =>{
             alert(`Rất tiếc!!! Người chơi Player ${activePlayer + 1} đã quay trúng số 1`);
          }, 10);
          this.nextPlayer();
        } else {
          this.currentScore = this.currentScore + dice1 + dice2;
        }
      } else {
        alert('Vui Lòng nhấn vào nút New Game');
      }
    },
    handleHoldScore() {
      if(this.isPlaying) {
        let {scoresPlayer, activePlayer, currentScore} = this;
        let scoreOld = scoresPlayer[activePlayer];
        // let cloneScoresPlayer = [...scoresPlayer];
        // cloneScoresPlayer[activePlayer] = scoreOld + currentScore;
        
        // this.scoresPlayer = cloneScoresPlayer;

        this.$set(this.scoresPlayer, activePlayer, scoreOld + currentScore);

        if(!this.isWinner){
          this.nextPlayer();
        } else {

        }

        // console.log(cloneScoresPlayer);
        // this.scoresPlayer[activePlayer] = scoreOld + currentScore;
        // this.scoresPlayer[this.activePlayer] = this.scoresPlayer[this.activePlayer] + this.currentScore;
      } else {
        alert('Vui Lòng nhấn vào nút New Game');
      }
      console.log('handleHoldScore App.vue');
    },
    handleChangeFinalScore(e) {
      var number = parseInt(e.target.value);
      if (isNaN(number)) {
        this.finalScore = '';
      } else {
        this.finalScore = number;
      }

      // console.log(parseInt(e.target.value));
    }
  },
  computed: {
    isWinner() {
      let { scoresPlayer, finalScore} = this;
      if (scoresPlayer[0] >= finalScore || scoresPlayer[1] >= finalScore) {
        this.isPlaying = false;
        return true;
      };
      return false;
    }
  }
}
</script>

<style>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    
}

.clearfix::after {
    content: "";
    display: table;
    clear: both;
}

body {
    background-image: linear-gradient(rgba(62, 20, 20, 0.4), rgba(62, 20, 20, 0.4)), url('/public/assets/back.jpg');
    background-size: cover;
    background-position: center;
    font-family: Lato;
    font-weight: 300;
    position: relative;
    height: 100vh;
    color: #555;
}

.wrapper {
    width: 1000px;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background-color: #fff;
    box-shadow: 0px 10px 50px rgba(0, 0, 0, 0.3);
    overflow: hidden;
}
</style>

<template>
  <div id="app">
    <div class="timer" ref="timer">0:0:0</div>
    <div class="board">
      <Card
        v-for="(item, index) in cards"
        :class="{ active: item.active }"
        @clickCard="openCard"
        :index="index"
        :name="item.name"
        :key="index + item"
      >{{ item.name }}</Card>
    </div>
    <div class="startgame">
      <Btn @click.native="startGame" />
    </div>
    <Error v-if="error" />
    <div class="leaderboard">
      <Title />
      <NoRes v-if="timerResult.length == 0" />
      <ResultList :list="timerResult" />
    </div>
  </div>
</template>

<script>
import { cards } from '@/database/cards';
import Card from '@/components/Card.vue';
import Btn from '@/components/Button.vue';
import Error from '@/components/Error.vue';
import ResultList from '@/components/ResultList.vue';
import Title from '@/components/Title.vue';
import NoRes from '@/components/NoRes.vue';

export default {
  name: 'App',
  components: {
    Card,
    Btn,
    Error,
    ResultList,
    Title,
    NoRes
  },
  mounted() {
    this.cards = this.arrayConcat(cards);
  },
  data() {
    return {
      cards: [],
      counter: 0,
      points: 0,
      openCardIndex: [],
      firstCard: null,
      secondCard: null,
      error: false,
      start: false,
      timerStart: false,
      timerResult: []
    }
  },
  methods: {
    startGame() {
      this.start = true;
      this.error = false;
      this.timer();
    },
    timer() {
      if (!this.timerStart) {
        this.timerStart = true;
        let timeMinut = 0 * 60
        let timer = setInterval(() => {
          let seconds = timeMinut % 60
          let minutes = timeMinut / 60 % 60
          let hour = timeMinut / 60 / 60 % 60
          let strTimer = `${Math.trunc(hour)}:${Math.trunc(minutes)}:${seconds}`;
          this.$refs.timer.innerHTML = strTimer;

          if (this.points == 9) {
            clearInterval(timer);
            this.timerResult.push(this.$refs.timer.innerHTML);
            this.$refs.timer.innerHTML = '0:0:0';
            this.restart();
          }

          ++timeMinut;
        }, 1000);
      }
    },
    arrayConcat(array) {
      let newArr = [];
      array.forEach((item) => {
        let obj = {};
        obj.name = item;
        obj.active = false;
        newArr.push(obj);
      });
      return newArr.sort(() => Math.random() - 0.5);
    },
    closeCard() {
      setTimeout(() => {
        this.openCardIndex.forEach((item) => {
          this.cards[item].active = false;
        });
      }, 1200)
    },
    restart() {
      setTimeout(() => {
        this.counter = 0;
        this.points = 0;
        this.timerStart = false;
        this.start = false;
        this.firstCard = null;
        this.secondCard = null;
        this.openCardIndex = [];
        this.cards.forEach((item) => {
          item.active = false;
        });
      }, 1200);
    },
    reset() {
      setTimeout(() => {
        this.counter = 0;
        this.firstCard = null;
        this.secondCard = null;
        this.openCardIndex = [];
      }, 1200);
    },
    openCard(data) {
      if (!this.start) {
        this.error = true;
        return;
      }

      let elIndex = data.index;
      let elName = data.name;

      if (this.cards[elIndex].active) {
        return;
      }

      this.counter++;

      if (this.counter <= 2) {
        if (!this.firstCard) {
          this.firstCard = elName;
        } else {
          this.secondCard = elName;
        }
        this.openCardIndex.push(elIndex);
        this.cards[elIndex].active = true;

        if (this.counter == 2) {
          if (this.firstCard == this.secondCard) {
            this.points++;
          } else {
            this.closeCard();
          }
          this.reset();
        }
      }
    }
  }
}
</script>

<style>
*,
*::after,
*::before {
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
}

blockquote,
body,
dd,
dl,
figcaption,
figure,
h1,
h2,
h3,
h4,
li,
ol,
p,
ul {
  margin: 0;
}

body,
html {
  height: 100%;
}

body {
  font-family: Arial, Helvetica, sans-serif;
}

ol,
ul {
  padding: 0;
}

a {
  font-family: inherit;
  text-decoration: none;
  color: inherit;
}

img {
  max-width: 100%;
  height: auto;
}

button {
  cursor: pointer;
  border: none;
}

button,
input,
select,
textarea {
  font-family: inherit;
}

.board {
  width: 630px;
  margin: 0 auto;
  display: flex;
  justify-content: space-between;
  flex-wrap: wrap;
}

.card {
  width: 100px;
  height: 120px;
  margin-bottom: 5px;
  border-radius: 10px;
  background: #417ccc;
  cursor: pointer;
  display: flex;
  justify-content: center;
  align-items: center;
  color: white;
  font-weight: 500;
  font-size: 24px;
}

.card span {
  display: none;
}

.card.active span {
  display: block;
}

.timer {
  padding-top: 50px;
  margin-bottom: 50px;
  text-align: center;
  font-weight: 500;
  font-size: 30px;
}

.startgame {
  padding-top: 50px;
  text-align: center;
  margin-bottom: 25px;
}

.startbtn {
  width: 150px;
  height: 30px;
  background: orange;
  font-size: 20px;
  font-weight: 500;
}

.errormessage {
  text-align: center;
  color: red;
  margin-bottom: 40px;
}

.leaderboard {
  text-align: center;
}

.leaderboard__title {
  margin-bottom: 15px;
}

.reslist li {
  font-size: 20px;
  margin-bottom: 5px;
}

.leaderboard__empty {
  text-align: center;
  font-size: 20px;
  color: red;
}
</style>

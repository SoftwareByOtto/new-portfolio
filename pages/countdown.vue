<template>
  <div>
    <b-container class="mt-3">
      <b-row class="text-center">
        <b-col md="4">
          <h1>COUNT</h1>
          <p v-if="message">{{ message }}</p>
          <div v-if="!gameStarted">
            <b-button @click="pickVowel">Pick a vowel</b-button>
            <b-button @click="pickConsonant">Pick a consontant</b-button>
          </div>
        </b-col>
        <b-col md="4"
          ><youtube
            @ready="ready"
            video-id="M2dhD9zR6hk"
            :player-vars="{
              showinfo: 0,
              controls: 0,
              start: 2,
              rel: 0,
              modestbranding: 1
            }"
            player-width="300"
            player-height="200"
          ></youtube
        ></b-col>
        <b-col md="4">
          <h1>DOWN</h1>
          <b-row>
            <b-col>
              <h3>Round</h3>
              <h1>{{ yourRound }}</h1>
            </b-col>
            <b-col>
              <h3>Score</h3>
              <h1>{{ yourScore }}</h1>
            </b-col>
            <b-col>
              <h3>Seconds</h3>
              <h1>{{ timeLeft }}</h1>
            </b-col>
          </b-row>
        </b-col>
      </b-row>
      <img
        v-if="!currentLetters.length && !gameStarted"
        src="https://www.standard.co.uk/s3fs-public/thumbnails/image/2019/11/21/14/rachelrileyextra211119a-1.jpg"
        width="400px"
        class="mt-3"
      />
      <h3 v-if="currentLetters.length">Your letters are:</h3>
      <p>
        <b-button
          @click="selectLetter(index)"
          class="letter"
          v-for="(letter, index) in currentLetters"
          :key="`a${letter}${index}`"
          >{{ letter }}</b-button
        >
      </p>
      <div v-if="gameStarted">
        <h3 v-if="yourWord.length">Your word is:</h3>
        <p>
          <b-button
            @click="deselectLetter(index)"
            class="letter"
            v-for="(letter, index) in yourWord"
            :key="`b${letter}${index}`"
            >{{ letter }}</b-button
          >
        </p>
        <b-button @click="submitWord">Submit Word</b-button>
      </div>
    </b-container>
  </div>
</template>

<script>
import _ from "lodash";
import words from "@/static/words.json";
import scrabble from "@nosweat/scrabble";
export default {
  data() {
    return {
      currentLetters: [],
      yourWord: [],
      vowels: "aeiou",
      consonants: "bbbcccdddfffggghhjkllmmnnpppqrrrssstttvwxyz",
      timeLeft: 30,
      timerInterval: null,
      dictionary: words,
      gameStarted: false,
      yourScore: 0,
      yourRound: 1,
      answers: [],
      message:
        "Welcome to countdown! Choose consonant or vowels. Click on letters to select or deselect. Click submit word when fininshed!"
    };
  },
  methods: {
    ready(event) {
      this.player = event.target;
      this.player.seekTo(2);
      this.player.pauseVideo();
    },
    pickVowel() {
      this.currentLetters.push(_.sample(this.vowels.split("")));
    },
    pickConsonant() {
      this.currentLetters.push(_.sample(this.consonants.split("")));
    },
    selectLetter(index) {
      this.gameStarted &&
        this.yourWord.push(this.currentLetters.splice(index, 1)[0]);
    },
    deselectLetter(index) {
      this.currentLetters.push(this.yourWord.splice(index, 1)[0]);
    },
    submitWord() {
      if (this.dictionary.includes(this.yourWord.join(""))) {
        const score = this.yourWord.length;
        this.yourScore += score;
        this.message = `Well done, you scored ${score} points!`;
      } else {
        this.message = `I'm afraid ${this.yourWord.join("") ||
          "that"} isn't a word, so you get 0 points.`;
      }
      this.message = this.message.concat(
        ` However, you could have said ${this.answers[0]}, ${this.answers[1]} or ${this.answers[2]}.`
      );
      this.currentLetters = [];
      this.yourWord = [];
      this.gameStarted = false;
      this.yourRound++;
    },
    startTimer() {
      this.timeLeft = 30;
      this.timerInterval = setInterval(() => {
        console.log("time ticking");
        this.timeLeft--;
        if (this.timeLeft <= 0) {
          this.submitWord();
        }
      }, 1000);
    }
  },
  watch: {
    currentLetters() {
      if (this.currentLetters.length === 10) {
        this.gameStarted = true;
      }
    },
    gameStarted() {
      if (this.gameStarted) {
        this.startTimer();
        this.player.playVideo();
        this.answers = scrabble
          .find(this.currentLetters.join(""))
          .sort((a, b) => b.length - a.length);
      } else {
        clearInterval(this.timerInterval);
        this.player.seekTo(2);
        this.player.pauseVideo();
      }
    }
  }
};
</script>

<style scoped>
.letter {
  padding: 15px;
  margin: 10px;
  font-size: 30px;
  background-color: rgb(22, 81, 184);
  color: white;
  box-shadow: 10px 10px 5px grey;
  text-transform: uppercase;
  font-weight: bold;
  text-decoration-style: none;
  display: inline-block;
  width: 75px;
  border: lightblue 2px solid;
  font-family: Avenir;
}
</style>

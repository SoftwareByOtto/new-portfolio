<template>
  <div>
    <b-container>
      <h1>Countdown!</h1>
      <h2>Round {{ yourRound }}</h2>
      <h2>Your score is {{ yourScore }}</h2>
      <p v-if="message">{{ message }}</p>
      <div v-if="!gameStarted">
        <b-button @click="pickVowel">Pick a vowel</b-button>
        <b-button @click="pickConsonant">Pick a consontant</b-button>
      </div>
      <h3 v-if="currentLetters.length">Your letters are:</h3>
      <p class="mt-5">
        <span
          @click="selectLetter(index)"
          class="letter"
          v-for="(letter, index) in currentLetters"
          :key="`a${letter}${index}`"
          >{{ letter }}</span
        >
      </p>
      <div class="mt-5" v-if="gameStarted">
        <h3 v-if="yourWord.length">Your word is:</h3>
        <p class="mt-5">
          <span
            @click="deselectLetter(index)"
            class="letter"
            v-for="(letter, index) in yourWord"
            :key="`b${letter}${index}`"
            >{{ letter }}</span
          >
        </p>
        <h3 class="mt-5">{{ timeLeft }} seconds left.</h3>
        <b-button @click="submitWord" class="mt-5">Submit Word</b-button>
      </div>
    </b-container>
  </div>
</template>

<script>
import _ from "lodash";
export default {
  data() {
    return {
      currentLetters: [],
      yourWord: [],
      vowels: "aeiou",
      consonants: "bbbcccdddfffggghhjkllmmnnpppqrrrssstttvwxyz",
      timeLeft: 30,
      timerInterval: null,
      dictionary: ["dogs"],
      gameStarted: false,
      yourScore: 0,
      yourRound: 1,
      message:
        "Welcome to countdown! Choose consonant or vowels. Click on letters to select or deselect. Click submit word when fininshed!"
    };
  },
  methods: {
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
      if (this.dictionary.includes(this.yourWord.join())) {
        const score = this.yourWord.length;
        this.yourScore += score;
        this.message = `Well done, you scored ${score} points`;
      } else {
        this.message = "Incorrect word. You scored 0 points.";
      }
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
        if (this.timeLeft < 0) {
          this.submitWord();
        }
      }, 1000);
    }
  },
  computed: {},
  watch: {
    currentLetters() {
      if (this.currentLetters.length === 10) {
        this.gameStarted = true;
      }
    },
    gameStarted() {
      if (this.gameStarted) {
        this.startTimer();
      } else {
        clearInterval(this.timerInterval);
      }
    }
  }
};
</script>

<style scoped>
.letter {
  padding: 30px;
  margin: 10px;
  font-size: 30px;
  background-color: rgb(22, 81, 184);
  color: white;
}
</style>

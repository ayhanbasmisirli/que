<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template v-slot:lead>
        {{ currentQuestion.question }}
      </template>

      <hr class="my-4" />

      <b-list-group v-for="(answer, index) in shuffleAnswersArr" :key="index">
        <b-list-group-item
          @click="selectAnswer(index)"
          :class="answerClass(index)"
          >{{ answer }}</b-list-group-item
        >
      </b-list-group>
      <b-button
        variant="primary"
        @click="submitAnswer"
        :disabled="selectedIndex === null && answered"
        >Submit</b-button
      >
      <b-button variant="success" @click="next">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from "lodash";
export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function,
  },
  data() {
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffleAnswersArr: [],
      answered: false,
    };
  },
  computed: {
    answers() {
      let answers = [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer,
      ];

      return answers;
    },
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.answered = false;
        this.shuffleAnswers();
      },
    },
  },

  methods: {
    selectAnswer(index) {
      this.selectedIndex = index;
    },
    submitAnswer() {
      let isCorrect = false;
      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true;
      }
      this.answered = true;
      this.increment(isCorrect);
    },
    shuffleAnswers() {
      let answers = [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer,
      ];
      this.shuffleAnswersArr = _.shuffle(answers);
      this.correctIndex = this.shuffleAnswersArr.indexOf(
        this.currentQuestion.correct_answer
      );
    },
    answerClass(index) {
      let answerClass = "";

      if (!this.answered && this.selectedIndex === index) {
        answerClass = "selected";
      } else if (this.answered && this.correctIndex === index) {
        answerClass = "correct";
      } else if (
        this.answered &&
        this.selectedIndex === index &&
        this.correctIndex !== index
      ) {
        answerClass = "incorrect";
      }
      return answerClass;
    },
  },
};
</script>
<style scoped>
.list-group {
  margin-bottom: 20px;
}
.list-group-item:hover {
  background-color: #eeee;
  cursor: pointer;
}
.selected {
  background-color: lightblue;
}
.correct {
  background-color: rgb(138, 195, 138);
}
.incorrect {
  background-color: rgb(233, 170, 170);
}
.btn {
  margin-left: 10px;
}
</style>

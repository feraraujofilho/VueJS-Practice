<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template v-slot:lead>{{ currentQuestion.question }}</template>

      <hr class="my-4" />

      <b-list-group>
        <b-list-group-item
          v-for="answer in answers"
          :key="answer"
          @click="selectAnswer(answer)"
          :class="answerClass(answer)"
        >{{ answer }}</b-list-group-item>
      </b-list-group>

      <b-button
        variant="primary"
        @click="submitAnswer"
        :disabled="selectedAnswer === null || answered"
      >Submit</b-button>
      <b-button @click="next" variant="success">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from "lodash";
export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function
  },
  data() {
    return {
      selectedAnswer: null,
      correctAnswer: null,
      shuffledAnswers: [],
      answered: false
    };
  },
  computed: {
    answers() {
      let answers = [...this.currentQuestion.incorrect_answers];
      answers.push(this.currentQuestion.correct_answer);
      return answers;
    }
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedAnswer = null;
        this.answered = false;
        this.shuffleAnswers();
      }
    }
  },
  methods: {
    selectAnswer(answer) {
      this.selectedAnswer = answer;
      console.log(answer);
    },
    shuffleAnswers() {
      let answers = [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer
      ];
      this.shuffledAnswers = _.shuffle(answers);
      this.correctAnswer = this.currentQuestion.correct_answer;
    },
    submitAnswer() {
      let isCorrect = false;

      if (this.selectedAnswer === this.correctAnswer) {
        isCorrect = true;
      }
      this.answered = true;

      this.increment(isCorrect);
    },
    answerClass(answer){
      let answerClass = ""

      if(!this.answered && this.selectedAnswer === answer){
        answerClass = "selected"
      } else if (this.answered && this.correctAnswer=== answer ){
        answerClass = "correct"
      } else if(this.answered && this.selectedAnswer === answer && this.correctAnswer !== answer){
        answerClass = "incorrect"
      }

      return answerClass
    }
  }
};
</script>

<style scoped>
.list-group {
  margin-bottom: 15px;
}

.list-group-item:hover {
  background: #eee;
  cursor: pointer;
}

.btn {
  margin: 0 5px;
}

.selected {
  background-color: lightblue;
}

.correct {
  background-color: lightgreen;
}

.incorrect {
  background-color: red;
}
</style>

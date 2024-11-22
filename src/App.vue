<script setup>
import { ref, computed } from 'vue';

const quizCompleted = ref(false); // Tracks if the quiz is complete
const currentQuestion = ref(0); // Tracks the current question index
const questions = ref([
  {
    question: 'What is Vue.js?',
    answer: 0,
    options: ['A front-end framework', 'A library', 'An ice cream maker'],
    selected: null,
  },
  {
    question: 'What is Vuex?',
    answer: 2,
    options: [
      'A Vue with an X',
      'A cheese selection',
      'State management library',
    ],
    selected: null,
  },
  {
    question: 'What is Vue Router used for?',
    answer: 1,
    options: ['Walking in space', 'A routing library for Vue.js', 'Burger sauce'],
    selected: null,
  },
]);

// Calculate the total score
const score = computed(() => {
  return questions.value.reduce(
    (total, question) =>
      total + (question.selected === question.answer ? 1 : 0),
    0
  );
});

// Get the current question
const getCurrentQuestion = computed(() => {
  const question = questions.value[currentQuestion.value];
  question.index = currentQuestion.value;
  return question;
});

// Handle selecting an answer
const setAnswer = (index) => {
  questions.value[currentQuestion.value].selected = index;
};

// Navigate to the next question or finish the quiz
const nextQuestion = () => {
  if (currentQuestion.value < questions.value.length - 1) {
    currentQuestion.value++;
  } else {
    quizCompleted.value = true; // Mark quiz as completed
  }
};

// Reset the quiz
const resetQuiz = () => {
  quizCompleted.value = false;
  currentQuestion.value = 0;
  questions.value.forEach((question) => (question.selected = null));
};

// Get dynamic classes for options
const getOptionClass = (index) => {
  const question = getCurrentQuestion.value;
  if (question.selected === null) return 'option';
  if (index === question.answer) return 'option correct';
  if (index === question.selected) return 'option wrong';
  return 'option disabled';
};
</script>

<template>
  <main class="app">
    <h1>The Quiz</h1>
    <section v-if="!quizCompleted" class="quiz">
      <div class="quiz-info">
        <span class="question">{{ getCurrentQuestion.question }}</span>
        <span class="score">
          Score ({{ score }} / {{ questions.length }})
        </span>
      </div>
      <div class="options">
        <label
          v-for="(option, index) in getCurrentQuestion.options"
          :key="index"
          :for="`option-${getCurrentQuestion.index}-${index}`"
          :class="getOptionClass(index)"
        >
          <input
            type="radio"
            :id="`option-${getCurrentQuestion.index}-${index}`"
            :name="`question-${getCurrentQuestion.index}`"
            :value="index"
            :disabled="getCurrentQuestion.selected !== null"
            @click="setAnswer(index)"
          />
          <span>{{ option }}</span>
        </label>
      </div>
      <button @click="nextQuestion" :disabled="getCurrentQuestion.selected === null">
        {{ getCurrentQuestion.index === questions.length - 1
          ? 'Finish'
          : 'Next Question' }}
      </button>
    </section>

    <!-- Display results when the quiz is completed -->
    <section v-else class="results">
      <h2>Quiz Completed!</h2>
      <p>Your score: {{ score }} / {{ questions.length }}</p>
      <button @click="resetQuiz">Retry</button>
    </section>
  </main>
</template>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Montserrat', sans-serif;
}
body {
  background-color: #271c36;
  color: #fff;
}
.app {
  padding: 20px;
}
.quiz-info {
  margin-bottom: 20px;
}
.options {
  margin-bottom: 20px;
}
.option {
  cursor: pointer;
  margin: 5px 0;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 5px;
  display: block;
}
.option.correct {
  background-color: #d4edda;
  border-color: #c3e6cb;
}
.option.wrong {
  background-color: #f8d7da;
  border-color: #f5c6cb;
}
.option.disabled {
  opacity: 0.6;
  cursor: not-allowed;
}
button {
  padding: 10px 20px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}
button:disabled {
  background-color: #6c757d;
  cursor: not-allowed;
}
.results {
  text-align: center;
}
</style>

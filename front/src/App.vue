
<template>
  <div class="quiz-container">
    <h1 class="quiz-title">Quiz</h1>
    <ul v-if="question" class="quiz-question-list">
      <li class="quiz-question-item">
        <div class="quiz-question-text">{{ question.text }}</div>
        <ol class="quiz-answer-list">
          <li v-for="answer in question.answers" :key="answer" class="quiz-answer-item">
            <button class="choices" :disabled="disableButtons" @click="selectedAnswer = answer; checkAnswer()">{{ answer }}</button>
          </li>
        </ol>
      </li>
    </ul>
    <div class="res" style="height: 5vh;">
    <p v-if="result" class="quiz-result" :class="{ 'quiz-result-incorrect': result === 'Incorrect', 'quiz-result-correct': result === 'Correct' }" ref="result">{{ result }}</p>
    </div>
    <div v-if="result" class="move-to-next-question">
      <button v-if="result" @click="moveToNextQuestion()">Move to Next Question</button>
    </div>
  </div>
</template>
<script>
import axios from 'axios';

export default {
  data() {
    return {
      question: null,
      selectedAnswer: '',
      result: 'Choose one',
      disableButtons: false,

    };
  },
  created() {
    this.fetchQuiz();
  },
  methods: {
    async fetchQuiz() {
  try {
    const res = await axios.get('http://localhost:3000/api/random-quiz');
    this.question = res.data[0];
    console.log(this.question.id);

    // Randomize the placement of answers
    this.question.answers = this.shuffleAnswers(this.question.answers);
  } catch (error) {
    console.error(error);
  }
},
shuffleAnswers(answers) {
  for (let i = answers.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [answers[i], answers[j]] = [answers[j], answers[i]];
  }
  return answers;
},
    moveToNextQuestion() {
  location.reload();
    },
    async checkAnswer() {
      if(this.disableButtons){
        return;
      }
      try {
        const res = await axios.post('http://localhost:3000/api/compareAnswers', {
          questionId: this.question.id,
          answer: this.selectedAnswer
        });
        this.result = res.data.result;
        if (res.data.result === "Correct") {
      document.querySelectorAll(".choices").forEach(btn => {
        btn.classList.remove("clicked");
        this.disableButtons = true;
      });
      document.querySelector(`[data-answer="${this.selectedAnswer}"]`).classList.add("clicked");
      console.log(this);
    }
        console.log(this.result);
      } catch (error) {
        console.error(error);
      }
    }
  }
};
</script>
<style>
body{
  margin: 0;
    height: 100vh;
   /* background: #b6b1ab70;*/
    background: linear-gradient(to bottom, rgba(22, 182, 221, 1) 0%, rgba(139, 217, 203, 1) 100%);
    /* background-image: radial-gradient(circle at top right, #fffffffb, red, #d5ede9); */
    /* background-image: radial-gradient(circle at top right, #d8d49afb, #d2bca2, #358242);
    background: linear-gradient(to bottom, #ffd700, #e5b700);
    background: linear-gradient(to right, #decead, #ccb583, #eed09a); */
    
    /* background-image: -webkit-linear-gradient(29deg, #fffffffb 0%, #d5ede9 100%); */
}
  .quiz-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 90vh;
  }
  
  .quiz-title {
    font-size: 36px;
    font-weight: bold;
    margin-bottom: 20px;
  }
  
  .quiz-question-list {
    list-style: none;
    padding: 0;
    margin: 0;
  }
  
  .quiz-question-item {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin-bottom: 20px;
  }
  
  .quiz-question-text {
    font-size: 24px;
    margin-bottom: 20px;
  }
  
  .quiz-answer-list {
    list-style: none;
    padding: 0;
    margin: 0;
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  
  .quiz-answer-item {
    margin-bottom: 10px;
  }
  
  .quiz-result {
    font-size: 24px;
    margin-top: 20px;
  }
  .quiz-result-correct{
    color: #28aa63;
  }
  .quiz-result-incorrect{
    color: #ce0000;;
  }
  
  .choices {
    width: 120px;
    padding: 10px 20px;
    border: 2px solid gray;
    border-radius: 10px;
    background-color: white;
    font-size: 18px;
    cursor: pointer;
  }
  
  .choices:hover {
    background-color: gray;
    color: white;
  }
  .move-to-next-question {
    text-align: center;
    margin-top: 150px;
  }

  .move-to-next-question button {
    padding: 10px 20px;
    background-color: #414742;
    color: white;
    border: none;
    border-radius: 5px;
    font-size: 18px;
    cursor: pointer;
  }

  .move-to-next-question button:hover {
    background-color: #657d65;
  }
</style>
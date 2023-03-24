<template v-if="this.question">
  <h1>{{ this.question }}</h1>
  <template v-for="(answer, index) of this.answers" v-bind:key="index">
    <input 
      type="radio" 
      :id="`id-${index}`"
      name="options" 
      :value="answer"
      v-model="this.chosenAnswer"
      :disabled="this.answerSubmited">

    <label v-html="answer" :for="`id-${index}`"></label><br>
  </template>

  <button 
      v-if="!this.answerSubmited" 
      class="send" 
      type="button" 
      @click="this.submitAnswer()"> Confirmar 
    </button>

    <section v-if="this.answerSubmited" class="result">
      <template v-if="this.chosenAnswer == this.correctAnswer">
        <h4>&#9989; Parabéns, a resposta "{{ this.correctAnswer }}" está correta.</h4>
      </template>
      <template v-else>
        <h4>&#10060;  Que pena, a resposta está errada. A resposta correta é "{{ this.correctAnswer }}".</h4>
      </template>
      <button class="send" type="button" @click="this.getNewQuestion()">Próxima pergunta</button>
    </section>
</template>

<script>
export default {
  name: 'App',
  components: {
  },
  data() {
    return {
      question: null,
      correctAnswer: null,
      incorrectAnswers: [],
      chosenAnswer: null,
      answerSubmited: false
    }
  },
  methods: {
    resetProps() {
      this.question = null
      this.chosenAnswer = null
      this.answerSubmited = false     
    },
    submitAnswer() {
      !this.chosenAnswer && alert('Escolha ao menos uma opção')
      this.answerSubmited = true;
    },
    getNewQuestion() {
      this.resetProps()
      const api = "https://opentdb.com/api.php?amount=1&category=18&type=multiple"
      this.axios.get(api).then((resp) => {
        try {
          if (resp.status !== 200) {
            throw 'Ops there is an error on request'
          }
          const data = resp.data.results[0]

          this.question = data.question
          this.incorrectAnswers = data.incorrect_answers
          this.correctAnswer = data.correct_answer
        } catch (err) {
          console.error(err)
        }
      })
    }
  },
  computed: {
    answers() {
      const leng = this.incorrectAnswers.length + 1 
      const aleart = Math.round(Math.random() * leng)

      const answers = this.incorrectAnswers
      answers.splice(aleart, 0, this.correctAnswer)
      return answers
    }
  },
  created() {
    this.getNewQuestion()
  }

}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto;
  max-width: 960px;

  input[type=radio] {
    margin: 12px 4px;
  }

  button.send {
    margin-top: 12px;
    height: 40px;
    min-width: 120px;
    padding: 0 16px;
    color: #fff;
    background-color: #1867c0;
    border: 1px solid #1867c0;
    cursor: pointer;
  }
}
</style>

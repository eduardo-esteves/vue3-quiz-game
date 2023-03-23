<template>
  <div>
    <h1>{{ this.question }}</h1>
    <input type="radio" name="options" value="Oculus">
    <label>Oculus</label><br>

    <input type="radio" name="options" value="Microsoft">
    <label>Microsoft</label><br>

    <input type="radio" name="options" value="Tobii">
    <label>Tobii</label><br>

    <button class="send" type="button">Confirmar</button>
  </div>
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
      incorrectAnswers: []
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

<template>
  <div class="main-container">
    <h1 class="main-header">Survey Squirrel</h1>
    <div v-if="surveyComplete" class="survey-complete">
      <p>Your survey has been created!</p>
      <button v-on:click="start_another_survey">Start Another Survey</button>
    </div>
    <div v-else>
      <h2 class="header-2-text">{{header2Text}}</h2>
      <div class="main-question-answer-container">
        <div :class="{hidden}" class="question-input-container">
          <div>What's your question?</div>
          <input v-model="question" placeholder="Type question here.">
          <button v-on:click="submit_question(question)">Save Question</button>
        </div>
        <div v-if="showQuestion" class="question-answer-container">
          <h2>Question 1</h2>
          <div class="show-question-question">{{question}}</div>
          <div class="answers-container">
            <div>Answers</div>
            <input v-model="answer" placeholder="Type answer here">
            <button v-on:click="submit_answer(answer)">Add Another Answer</button>
            <ul v-if="answersList.length > 0">
              <li v-for="answer in answersList">
                {{ answer }}
              </li>
            </ul>
          </div>
        </div>
        <div v-else-if="questionComplete" class=question-answer-container>
          <h2>Question 1</h2>
          <div class="show-question-question">{{question}}<span v-on:click="show_question_and_answer_for_review" class="show-answers-for-review"><img src="./../assets/down_arrow.png"></span></div>
        </div>
        <div v-else-if="showQuestionAndAnswer" class="question-answer-container">
          <h2>{{question}}</h2>
          <span class="down-arrow-image-for-review"><img src="./../assets/down_arrow.png"></span>
          <ul v-if="answersList.length > 0" class="review-ul-list">
            <li v-for="answer in answersList" class="list-answers-for-review">
              {{ answer }}
            </li>
          </ul>
        </div>
      </div>
      <div class="complete-survey-container">
        <button v-on:click="done" class="submit-button">{{submit}}</button>
        <button class="add-question-button">Add Question</button>
      </div>
    </div>
  </div>

</template>

<script>
export default {
  name: 'SurveyMain',
  data () {
    return {
      title: 'Survery Squirrel',
      header2Text: 'Welcome to Survey Squirrel. Let\'s build a survey!',
      question: '',
      answer: '',
      submit: 'Done',
      answersList: [],
      hidden: '',
      showQuestion: false,
      questionComplete: false,
      showQuestionAndAnswer: false,
      surveyComplete: false
    }
  },
  methods: {
    submit_question: function (question) {
      if (question === '') {
        return
      }
      this._data.question = question
      var lsData = {'question': question, 'answersList': []}
      localStorage.setItem('lsData', JSON.stringify(lsData))
      this._data.hidden = 'hidden'
      this._data.showQuestion = true
    },
    submit_answer: function (answer) {
      if (answer === '') {
        return
      }
      this._data.answersList.push(answer)
      var retrievedLsData = JSON.parse(localStorage.getItem('lsData'))
      // for some odd reason, it doesn't consistenly parses local storage above so the below conditional will catch it
      if (typeof retrievedLsData === 'string') {
        retrievedLsData = JSON.parse(retrievedLsData)
      }
      retrievedLsData.answersList.push(answer)
      var lsData = JSON.stringify(retrievedLsData)
      localStorage.setItem('lsData', JSON.stringify(lsData))
      this._data.answer = ''
    },
    show_question_and_answer_for_review: function () {
      this._data.questionComplete = false
      this._data.showQuestionAndAnswer = true
    },
    done: function () {
      console.log('done!')
      if (this.question === '') {
        return
      }
      if (this._data.submit === 'Done') {
        this._data.submit = 'Preview'
      } else if (this._data.submit === 'Preview') {
        this._data.submit = 'Submit'
        this._data.header2Text = 'Review Survey'
        this._data.showQuestion = false
        this._data.questionComplete = true
      } else {
        console.log('submit the survey')
        this._data.surveyComplete = true
      }
    },
    start_another_survey: function () {
      localStorage.clear()
      this._data.header2Text = 'Welcome to Survey Squirrel. Let\'s build a survey!'
      this._data.question = ''
      this._data.answer = ''
      this._data.submit = 'Done'
      this._data.answersList = []
      this._data.hidden = ''
      this._data.showQuestion = false
      this._data.questionComplete = false
      this._data.showQuestionAndAnswer = false
      this._data.surveyComplete = false
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
  @import 'SurveyMain.scss'
</style>

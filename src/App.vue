<template>
  <div class="main">
    <img src="./assets/logo.jpg" alt="Quiz Game Logo" class="title">

    <div class="newQuestion" v-if="this.questionInput" id="newQuestion">
      <h2>Unesite pitanja i označite točan odgovor za svako pitanje. 
        Nakon unosa pritisnite strelicu za rješavanje kviza i vidite koliko znanje imaju vaši prijatelji!</h2>
  
      <button @click="returnBack()" id="exitBtn" v-on:mouseover="hoverColor()" v-on:mouseout="hoverColor1()" class="exitButton material-icons md-32">{{ this.exitMsg }}</button>
      <input type="text" placeholder="Pitanje..." id="quest" autofocus>
      <div class="questionInput">
        <span><input type="text" placeholder="Odgovor 1" id="0" class="A0"><input type="radio" checked name="pitanje" value="0" id="A0"></span>
        <span><input type="text" placeholder="Odgovor 2" id="1" class="A1"><input type="radio" name="pitanje" value="1" id="A1"></span>
        <span><input type="text" placeholder="Odgovor 3" id="2" class="A2"><input type="radio" name="pitanje" value="2" id="A2"></span>
      </div>
      <button @click="createQuestion()" class="createQuestionButton">Unesi pitanje</button>
      
    </div>


    <div class="quizTest" v-else>
      <div class="input" v-if="!this.quizCompleted" id="input">

      <button @click="toggleQuestionInput()" id="newQuestButton">New Question</button>

      <p>Question: {{ this.questionNumber }}/{{ this.questions.length }}</p>

      <h1>{{ this.questions[this.currentQuestion].question }}</h1>


      <div class="labels">
        <label v-for="(option,index) in this.questions[this.currentQuestion].options" 
      :key="option" 
      :class="{correct 
      : this.questions[this.currentQuestion].selected != null && this.questions[this.currentQuestion].answer == index, 
      incorrect: this.questions[this.currentQuestion].selected != null && this.questions[this.currentQuestion].answer != index}"
      >
        <input type="radio"
        id="radio"
        :name="this.currentQuestion" 
        :disabled="this.questions[this.currentQuestion].selected != null"
        :value="index"
        @change="selectedAnswer(index)">
        <span>{{ this.questions[this.currentQuestion].options[index] }}</span>
      </label>
      </div>
      <button v-on:click="nextQuestion()" class="nextButton">{{ this.quizButton }} <span class="material-icons">navigate_next</span></button>
      

      <h3 class="score">Score: {{ this.score }}/{{ this.questionNumber }}</h3>
      </div>
    <div class="lastPage" v-else>
      <h1>Završeno</h1>
      <h3>Score: {{ this.score }}/{{ this.questionNumber }} </h3>
      <h4>Uspješnost: {{ ((this.score/this.questionNumber)*100).toFixed(2)}}% </h4>
      
      <button @click="restart()" class="refreshButton"><span class="material-icons">replay</span></button>

    </div>
    </div>
  </div>
    

</template>

<script>

export default {
  data: function() {
    return {
      questions: [
      ],
      customArray: [],
      currentQuestion: 0,
      score: 0,
      quizCompleted: false,
      questionNumber: 0,
      questionInput : true,
      quizButton: 'Next Question',
      exitMsg: 'cancel'
    }
  },
  methods: {
    selectedAnswer: function(index) {
      this.questionNumber = this.currentQuestion
      document.getElementById('newQuestButton').style.opacity = "0"
      document.getElementById('newQuestButton').style.transitionDuration = "1s"

      this.questionNumber++
      if(this.questionNumber > this.questions.length - 1){
        this.quizButton = 'Finish'
      }
      this.questions[this.currentQuestion].selected = index
      if (this.questions[this.currentQuestion].answer == this.questions[this.currentQuestion].selected){
        this.score++
      }

    },
    nextQuestion: function() {

      if(this.questions[this.currentQuestion].selected == null){
        alert("Odaberite jedan odgovor!")
      }
      else{
         this.currentQuestion++

          if(this.questions.length === 0){
            return
          }
          else if(this.currentQuestion > this.questions.length - 1){
            document.getElementById('input').style.opacity = '0'
            document.getElementById('input').style.transitionDuration = '0.5s'
            var that = this
            window.setTimeout(function() {that.quizCompletion()},500)
          }
      }
    },
    quizCompletion: function() {
      this.quizCompleted = true
    },
    createQuestion: function() {
      console.log(document.getElementById("A0"))
      
      let ans_ind = null

      if(document.getElementById("A0").checked == true){
        ans_ind = 0
      }
      else if(document.getElementById("A1").checked == true){
        ans_ind = 1
      }
      else{
        ans_ind = 2
      }

      if(document.getElementById("quest").value && document.getElementById("0").value && document.getElementById("1").value && document.getElementById("2").value){
        this.questions.push({ 
      question: document.getElementById("quest").value, 
      answer: ans_ind,
      options: 
        [document.getElementById("0").value,
        document.getElementById("1").value,
        document.getElementById("2").value], 
      selected: null})

      document.getElementById("A"+ans_ind).checked = false
      document.getElementById("A0").checked = true
      document.getElementById("quest").value = ''
      document.getElementById("0").value = ''
      document.getElementById("1").value = ''
      document.getElementById("2").value = ''

      this.exitMsg = 'play_circle'


      }
      else{
        alert("Unesite sva polja!")
      }

            
    },
    hoverColor: function(){
      if(this.exitMsg == 'play_circle'){
        document.getElementById('exitBtn').style.color = 'var(--green)'
        document.getElementById('exitBtn').style.transitionDuration = '1s'
      }
      else{
        document.getElementById('exitBtn').style.color = 'var(--maroon)'
        document.getElementById('exitBtn').style.transitionDuration = '1s'
      }
    },
    hoverColor1: function(){
      document.getElementById('exitBtn').style.color = 'var(--blue)'
      document.getElementById('exitBtn').style.transitionDuration = '1s'
    },
    returnBack: function() {
      if(this.questions.length > 0) {
        document.getElementById('newQuestion').style.opacity = '0'
        document.getElementById('newQuestion').style.transitionDuration = '0.5s'
        var that = this
        window.setTimeout(function() {that.questionInput = !that.questionInput},500)
      }
      else{
        alert("Unesite pitanje i odgovore!")
      }
    },
    toggleQuestionInput: function(){
        document.getElementById('input').style.opacity = '0'
        document.getElementById('input').style.transitionDuration = '0.5s'
        var that = this
        window.setTimeout(function() {that.questionInput = !that.questionInput},500)
    },
    restart: function() {
      window.location.reload()
    }
  }
}

</script>

<style>
*{
  font-family: 'Roboto', sans-serif;
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  --blue: #482673;
  --green: rgb(19, 220, 46);
  --maroon: #ed0b70;
  --yellow: #fee36e;
}

.main{
  display: flex;
  flex-direction: column;
  align-items: center;
  background: rgb(255,255,255);
background: -moz-linear-gradient(top, rgb(255,255,255) 0%, rgb(204,204,204) 50%, rgb(181,181,181) 100%);
background: -webkit-linear-gradient(top, rgb(255,255,255) 0%, rgb(204,204,204) 50%, rgb(181,181,181) 100%);
background: linear-gradient(to bottom, rgb(255,255,255) 0%, rgb(204,204,204) 50%, rgb(181,181,181) 100%);
  height: 100vh;
}

.title{
  width: 20vw;
  height: auto;
  margin: 4vh 0 4vh 0;
}

.input{
  box-sizing: border-box;
  display: flex;
  opacity: 1;
  margin-bottom: 0;
  height: 45vh;
  flex-direction: column;
  align-items: center;
}

.labels{
  margin-top: 3vh;
}

.labels label{
  width: 30vw;
  height: 7vh;
  padding: 0 10px;
  background-color: #708090;
  color: #fff;
  transition: all 0.5 ease-in-out;
  border-radius: 6px;
  margin: 15px 0;
  cursor: pointer;
}

.input button:first-of-type{
  width: 15vw;
  height: 12vh;
  position: relative;
  top: -20px;
  font-size: 16px;
  opacity: 1;
  color: #fff;
  background-color: var(--blue);
  padding: 5px 20px;
}

.input h1{
  margin-top: 4vh;
  color: #000;
  border-bottom: 2px solid #708090;
  width: 40vw;
  height: auto;
  font-size: 28px;
  padding: 10px;
  border-radius: 6px;
  text-align: center;
}

label{
  display: flex;
  height: 7vh;
  flex-direction: row;
  align-items: center;
}
label span{
  margin-left: 5px;
}

.correct{
  color: #000 !important;
  background-color: var(--green) !important;
  border: 2px dashed rgb(192, 255, 171) ;
  font-weight: bold;
}

.nextButton{
  display: flex;
  align-items: center;
  color: #fff ;
  background-color: var(--green) ;
  justify-content: center;
  cursor: pointer;
  font-size: 18px;
  width: 40vw;
  margin-top: 20px;
  height: 12vh;
  padding: 15px 0 15px 10px;
}

.score{
  margin-top: 2vh;
  align-self: center;
  color: var(--yellow);
}
.incorrect{
  background-color: var(--maroon) !important;
  border: 2px dashed #FF94C2;
}

.newQuestion{
  display: flex;
  flex-direction: column;
  align-items: center;
  height: 80vh;
  width: 50vw;
  opacity: 1;
  transition-duration: 1s;
}

.newQuestion h2{
  margin-bottom: 50px;
  font-size: 16px;
  color: var(--blue);
  font-weight: lighter;
  width: 30vw;
}

.questionInput{
  display: flex;
  height: 22vh;
  margin: 6vh 0 6vh 0;
  flex-direction: column;
  align-items: center;
  justify-content: space-between;
}

#quest{
  width: 40vw;
  font-size: 20px;
  padding: 10px;
}

.A0, .A1, .A2{
  padding-left: 10px;
  

}


input[type="text"]{
  width: 25vw;
  height: 5vh;
  display: inline-block;
  outline: none;
  border-bottom: 2px solid  #708090;
  transition-duration: 0.5s;
  border-top: none;
  border-right: none;
  color: #000;
  border-left: none;
  background-color: transparent;
}

input[type="text"]:hover{
  background-color: rgba(200,189,255,0.4);
}

input[type="text"]:focus{
  background-color: rgba(200,189,255,0.4);
  border-bottom: 2px solid var(--blue);
}




#A0:checked::after, #A1:checked::after, #A2:checked::after{
  transition-duration: 0.5s;
  background-color: var(--green);
}

.input label{
  font-size: 16px;
}

.refreshButton{
  font-size: 32px;
  width: 40px;
  color: #000;
  background-color: #fff;
  border-radius: 50%;
}

#A0::after, #A1::after, #A2::after{
  content: '';
  width: 5px;
  height: 5px;
  border-radius: 50px;
  border: 2px solid white;
  padding: 3px;
  position: relative;
  left: -2px;
  display: inline-block;
  visibility: visible;
  background-color: var(--maroon);
  cursor: pointer;
}

button{
  background-color: #fff;
  color: var(--blue);
  border: 1px solid #aaa;
  border-radius: 10px;
  cursor: pointer;
}

.input button{
  border-radius: 10px;
  width: 10vw;
  height: 5vh;
}

.questionInput input[type="radio"]{
  margin-left: 20px;
}

.lastPage{
  display: flex;
  height: 30vh;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
}

.createQuestionButton{
  width: 10vw;
  background-color: var(--blue);
  border: 2px solid transparent;
  color: #FFF;
  font-size: 16px;
  cursor: pointer;
  padding: 10px;
  transition: all 0.2s ease-in-out;
}

.createQuestionButton:hover{

  border: 2px solid #fff;
}


.exitButton:focus{
  color: #fff;
}



.exitButton{
  position: absolute;
  border-radius: 50%;
  right: 31vw;
  color: var(--blue);
  background-color: transparent;
  transition-duration: 0.5s;
  border: none;;
  cursor: pointer;
  font-weight: bold;
  top: 28.5vh;
  font-size: 34px;
  width: auto;
  height: auto;
}
</style>


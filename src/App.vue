<template>
	<div id="app">
        <div class="wrapper clearfix">
            <players 
            v-bind:isWinner="isWinner"
            v-bind:activePlayer="activePlayer"
            v-bind:currentScore="currentScore"
            v-bind:scoresPlayer="scoresPlayer"/>
            
            <controls
                v-bind:isPlaying="isPlaying"
                v-bind:finalScore="finalScore"
                v-on:handleFinalScore="handleFinalScore"
                v-on:handeNewGame="handeNewGame" 
                v-on:RollDice="RollDice"
                v-on:handleHold="handleHold"
            />
            
            <dices
                v-bind:dice="dices" 
            /> 
            <popup-rule 
                v-on:handleConfirm="handleConfirm"
                v-bind:isOpenPopup="isOpenPopup" />
        </div>
	</div>
</template>

<script>
import Controls from './components/Controls.vue';
import Dices from './components/Dices.vue';
import players from './components/players' ; 
import PopupRule from './components/PopupRule.vue';

export default {
	name: 'app',
	data () {
		return {
            isPlaying: false,
            isOpenPopup: false,
            activePlayer: 0, //Ai là người chơi hiện tại? 
            scoresPlayer: [3, 5],
            dices: [1, 6],
            currentScore: 5,
            finalScore: 10 , 
           
		}
	},
	components: {
        players,
        Controls,
        Dices,
        PopupRule,

    },
    methods:{
        handleFinalScore(e){
            var number = parseInt(e.target.value) ; 
            if (isNaN(number)){
                this.finalScore = '' ; 
            }else {
                this.finalScore = number ; 
            }
        },
        nextPlayer(){
            this.currentScore = 0 ; 
            this.activePlayer = this.activePlayer == 0 ? 1 : 0 ; 
            console.log(this.activePlayer) ; 
        },
        handeNewGame(){
            console.log("new game Appvue") ; 
            this.isOpenPopup = true ; 
        } , 
        handleConfirm(){
            console.log("handleConfirm in app.vue") ; 
            this.isOpenPopup = false ; 
            this.isPlaying = true ; 
            this.activePlayer = 0  ; 
            this.scoresPlayer = [0 ,0] ; 
            this.currentScore = 0 ; 
            
            this.dices = [1, 1] ; 
        }, 
        RollDice(){
            if ( !this.isPlaying) {
                alert("Vui lòng bấm new game ") ; 
            }else{
                var a = Math.floor(Math.random() * 6) + 1 ; 
                var b = Math.floor(Math.random() * 6) + 1 ; 
                console.log(a ,b ) ; 
                this.dices = [a ,b ] ; 
                if ( a === 1 || b === 1){
                    setTimeout(function(){
                        alert("Rất tiếc bạn quay được 1 đ") ; 
                    }, 10)
                  this.nextPlayer() ; 
                }else {
                    this.currentScore += a + b ; 
                }
               
            }
          
        },
        handleHold(){
             if ( !this.isPlaying) {
                alert("Vui lòng bấm new game ") ; 
            }else{
                console.log("hand hold") ;
                let { scoresPlayer , activePlayer , currentScore} = this ; 
                let scoreOld = scoresPlayer[activePlayer] ; 
                let cloneScorePlayer = [...scoresPlayer] ; 
                    this.$set(this.scoresPlayer, activePlayer , scoreOld + currentScore) ; 
                if ( !this.isWinner){
                    this.nextPlayer() ; 
                }
                
               //this.scoresPlayer[this.activePlayer] += this.currentScore ;  
            }
        }

    }, 
    computed:{
        isWinner(){
            let { scoresPlayer , finalScore} = this; 
            if(scoresPlayer[0] >= finalScore || scoresPlayer[1] >= finalScore) {
                this.isPlaying = false ; 
                return true ; 
            }
            return false ; 

        }
    }

   
}
</script>

<style>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    
}

.clearfix::after {
    content: "";
    display: table;
    clear: both;
}

body {
    background-image: linear-gradient(rgba(62, 20, 20, 0.4), rgba(62, 20, 20, 0.4)), url(../public/assets/back.jpg);
    background-size: cover;
    background-position: center;
    font-family: Lato;
    font-weight: 300;
    position: relative;
    height: 100vh;
    color: #555;
}

.wrapper {
    width: 1000px;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background-color: #fff;
    box-shadow: 0px 10px 50px rgba(0, 0, 0, 0.3);
    overflow: hidden;
}

</style>
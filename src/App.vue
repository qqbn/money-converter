<template>
  <div id="app">
    <div id="first-page" class="first-page">
      <div class="header">
        <h1>Money <span class="euro">€</span>onverter</h1>
        <div class="theme-box">
          <img class="theme-img2" src="./assets/theme.svg" alt="theme" @click="themeChanger()">
        </div>
      </div>
      <div class="main-section">
        <div class="img-box">
          <img src="./assets/image.svg" alt="img">
        </div>
        <div class="info-box">
          <div class="text-box">
            <h2>How <span class="color-info">it</span> works?</h2>
          <div class="info-section">
            <p id="first-page-text" class="first-page-text">It's easy, you can check the currency exchange here. Do not be afraid, we will remember your last exchanges so that you can compare rates over time. All u have to do, its press the button below and starts, converting <br />
            <span class="pro-tip">Pro tip:</span> u you can click on a particular line to see the odds comparison </p>
          </div>
          </div>
        </div>
      </div>
      <div class="footer">
        <button class="btn" @click="smoothScroll()"></button>
      </div>
    </div>

      <div id="second-page" class="second-page">
        <div class="header2">
          <h1 class="h2">Money <span class="euro">€</span>onverter</h1>
          </div>
        <Formula  @newConvert="setData($event)"/>
        <List @isOpened="openBox($event)" @whichId="dataToBox($event)" :currencyData="currencyData"/>
      </div>
       <div id="compare-box" class="compare-box">

          <div id="compare-box-header" class="compare-box-header">
              <h5 id="compare-header" class="compare-header">Your comparision</h5>
          </div>
          <div class="compare-section">
            <div class="small-boxes">
                <div id="compare-small-box" class="compare-small-box">
                     <p class="compare-small-box-p"> {{this.historyDate}} <span>{{this.leftSelectHistory}} TO {{this.rigthSelectHisotry}} WAS {{this.currencyHistory}}</span></p>
                </div>
                <div id="compare-small-box" class="compare-small-box">
                    <p class="compare-small-box-p"> {{this.todayDate}}  <span>{{this.leftSelectHistory}} TO {{this.rigthSelectHisotry}} IS {{this.currencyToday}}</span></p>
                </div>
            </div>
            <div class="arrow-box">
              <img id="green-arrow" class="green-arrow" src="./assets/green-arrow.png" alt="arrow">
              <img id="red-arrow" class="red-arrow" src="./assets/red-arrow.png" alt="arrow">
              <button id="button" @click="closeBox()">CLOSE</button>
            </div>
          </div>

        </div>
  </div>
</template>

<script>
import Formula from './components/Formula.vue'
import List from './components/List.vue'
export default {
  name: 'App',
  components:{
    Formula,
    List,
  },
  data(){
    return{
      darkMode: '',
      dataHistory: [],
      leftSelectHistory: null,
      rigthSelectHisotry:null,
      historyDate: null,
      currencyHistory: null,
      todayDate: null,
      todayCurrency:null,
      currencyToday:null,
      currencyData: [],
    }
  },
  methods:{
    setData(n){
      this.currencyData=n;
    },
   smoothScroll(){
    var newTarget=document.querySelector('.h2');
    var targetPosition=newTarget.getBoundingClientRect().top;
    var startPosition=window.pageYOffset;
    var distance = targetPosition-startPosition;
    var startTime=null;

    function animation(currentTime){
        if(startTime===null) startTime=currentTime;
        var timeElapsed=currentTime-startTime;
        var run=ease(timeElapsed,startPosition,distance,2000);
        window.scrollTo(0,run);
        if(timeElapsed<2000) requestAnimationFrame(animation);
    }

    function ease(t,b,c,d){
        t /=d/2;
        if(t<1) return c/2*t*t+b;
        t--;
        return -c/2 *(t*(t-2)-1)+b;
    }

    requestAnimationFrame(animation);
    },
    themeChanger(){
      this.darkMode=localStorage.getItem("darkMode")
      if(this.darkMode!="enabled"){
        this.enableDarkMode();
      }else{
        this.disableDarkMode();
      }

    },
    enableDarkMode(){
      document.getElementById("first-page").classList.add('dark-mode');
      document.getElementById("second-page").classList.add('dark-mode');
      document.getElementById("first-page-text").classList.add('dark-mode');
      document.getElementById("compare-box").classList.add('dark-box');
      document.getElementById("compare-header").classList.add('dark-box');
      const paraTxt=document.querySelectorAll(".compare-small-box-p");
      paraTxt.forEach(element => {
        element.style.color="white";
      });
      document.getElementById("button").classList.add('dark-box');
      localStorage.setItem("darkMode","enabled");
    },
    disableDarkMode(){
      document.getElementById("first-page").classList.remove('dark-mode');
      document.getElementById("second-page").classList.remove('dark-mode');
      document.getElementById("first-page-text").classList.remove('dark-mode');
      document.getElementById("compare-box").classList.remove('dark-box');
      document.getElementById("button").classList.remove('dark-box');
      document.getElementById("compare-header").classList.remove('dark-box');
      const paraTxt=document.querySelectorAll(".compare-small-box-p");
      paraTxt.forEach(element => {
        element.style.color="black";
      });
      localStorage.setItem("darkMode","disabled");
    },
    openBox(isOpened){
      if(isOpened==false){
      document.getElementById("first-page").style.filter="blur(5px)";
      document.getElementById("second-page").style.filter="blur(5px)";
      document.getElementById("compare-box").style.visibility="visible";
      }
    },
    closeBox(){
      document.getElementById("first-page").style.filter="blur(0)";
      document.getElementById("second-page").style.filter="blur(0)";
      document.getElementById("compare-box").style.visibility="hidden";
      document.getElementById("green-arrow").classList.remove('show-arrow');
      document.getElementById("red-arrow").classList.remove('show-arrow');
    },
    dataToBox(n){
      this.dataHistory=this.currencyData;
      this.dataHistory.forEach(element => {
        if(element.id===n){
          this.historyDate=element.date;
          this.leftSelectHistory=element.leftCurrency;
          this.rigthSelectHisotry=element.rightCurrency;
          this.currencyHistory=element.currentRate;
        }
      });
      fetch(`https://free.currconv.com/api/v7/convert?q=${this.leftSelectHistory}_${this.rigthSelectHisotry}&compact=ultra&apiKey=244ce8c22fd118e3b024`)
            .then(res => res.json())
            .then(data => this.todayData(data))
            .catch(err => console.log(err.message));
    },
    todayData(data){
      this.currencyToday=data[Object.keys(data)[0]];
      console.log(this.currencyToday+'pobranie z api');
      this.todayDate=new Date().toISOString().slice(0,10);
      console.log(this.currencyHistory);
      console.log(this.currencyToday);
      if(this.currencyHistory<this.currencyToday){
              document.getElementById("red-arrow").classList.add("show-arrow");
            }else{
              document.getElementById("green-arrow").classList.add("show-arrow");
            }
    },
  },
  mounted(){
     if(localStorage.getItem("currencyHistory")){
        this.currencyData=JSON.parse(localStorage.getItem("currencyHistory"));
    }
    this.darkMode=localStorage.getItem("darkMode")
     if(this.darkMode==="enabled"){
       console.log(this.darkMode);
       this.enableDarkMode();
     }else{
       console.log(this.darkMode);
     }

    }
}
</script>

<style>

@import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@300;400&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Pacifico&display=swap');
.dark-mode{
  background-color: #30333d;
  color: white;
}
.dark-box{
  background-color: #3e424f !important;
  color: white !important;
}
.compare-box{
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    position: absolute;
    bottom: -90%;
    left: 50%;
    height: 500px;
    width: 400px;
    border-radius: 30px;
    visibility: hidden;
    box-shadow: 0px 0px 10px #30333d;
    background-color: #f7f9ff;
    transform: translate(-50%, 0);
}
.compare-box-header{
    width: 100%;
    height: 20%;
    text-align: center;
    font-weight: 400;
    font-size: 30px;
}.compare-box-header h5{
    font-weight: bold;
    font-size: 30px;
    color: black;
    text-decoration: underline;
    text-decoration-color: #0A8845;
    margin-top: 20px;
    font-family: 'Montserrat', sans-serif;
}
.compare-section{
    width: 100%;
    height: 80%;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
}
.small-boxes{
width: 100%;
height: 50%;
display: flex;
justify-content: center;
align-items: center;
}
.show-arrow{
  display: flex !important;
}
.compare-small-box{
    width: 50%;
    height: 100%;
    text-align: center;
    display: flex;
    justify-content: center;
    align-items: center;
    font-weight: 200;
    font-size: 24px;
    font-family: 'Montserrat', sans-serif;
}
.compare-small-box-p{
    color: black;
    font-weight: bold;
    margin: 0px 10px;
}
.arrow-box{
    width: 100%;
    height: 50%;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
}
.arrow-box img{
    height: 100px;
    width: 100px;
    display: none;
}
.arrow-box button{
    margin-top: 20px;
    width: 50px;
    height: 30px;
    border: none;
    background-color: transparent;
    background-repeat:no-repeat;
    cursor: pointer;
    color: black;
    border-bottom: 4px solid #0A8845;
    font-family: 'Montserrat', sans-serif;
    font-weight: bold;
}
*{
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
#app{
  width: 100%;
}
.first-page{
  height: 100vh;
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}
.header{
  height: 10vh;
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}
.header2{
display: flex;
justify-content: center;
align-items: center;
height: 10vh;
}
.euro{
  color: #BFB904;
  font-size: 60px;
}
h1{
  font-size: 45px;
  font-family: 'Pacifico', cursive;
  font-weight: 100;
  color: #0A8845;
  text-align: center;
  width: 950%;
}
.theme-box{
  position: absolute;
  right: 0;
  top: 0;
  width: 50px;
  height: 50px;
  display: flex;
  justify-content: center;
  align-items: center;
}
.theme-img2{
  height: 25px;
  width: 25px;
  cursor: pointer;
  display: flex;
}
.theme-box p{
  color: black;
  font-family: 'Montserrat', sans-serif;
  font-weight: 100;
  font-size: 14px;
}
.main-section{
  width: 100%;
  height: 80vh;
  display: flex;
  justify-content: space-around;
  align-items: center;
}
.img-box{
  height: 100%;
  width: 45%;
  display: flex;
  justify-content: center;
  align-items: center;
}
.img-box img{
  height: 90%;
  width: 90%;
}
.info-box{
  height: 100%;
  width: 45%;
  justify-content: center;
  display: flex;
  align-items: center;
}
.text-box{
  width: 90%;
  height: 80%;
  display: flex;
  justify-content: space-around;
  align-items: center;
  flex-direction: column;
  border-radius: 30px;
  box-shadow: #0A8845 0px 0px 3px 1px;
  border: 1px solid  #BFB904 ;
}
h2{
  height: 30%;
  width: 100%;
  text-align: center;
  font-size: 40px;
  font-family: 'Pacifico', cursive;
  color: #0A8845;
  font-weight: 100;
}
.color-info{
  color: #BFB904;
}
.pro-tip{
  font-family: 'Pacifico', cursive;
  font-size: 32px;
  border-bottom: 3px solid #0A8845;
   color: #BFB904;
}
.info-section{
height: 70%;
width: 100%;
font-size: 24px;
font-family: 'Montserrat', sans-serif;
}
p{
  text-align: center;
  color: #707070;
  font-weight: 100;
}
.footer{
  width: 100%;
  height: 10vh;
  display: flex;
  justify-content: center;
  align-items: center;
}
.h2{
  width: 100%;
  font-size: 45px;
  font-family: 'Pacifico', cursive;
  font-weight: 100;
  color: #0A8845;
  text-align: center;
  text-align: center;
}
.btn{
  height: 70px;
  width: 70px;
  border-radius: 50%;
  margin-bottom: 20px;
  background-image: url(./assets/arrow-button.svg);
  background-position: center;
  background-repeat: no-repeat;
  background-size: 40px 40px;
  background-color: #0A8845;
  border: none;
  transform: rotate(180deg);
  cursor: pointer;
}
.btn:hover{
  background-color: #07753b;
   background-image: url(./assets/arrow-button.svg);
  background-position: center;
  background-repeat: no-repeat;
  background-size: 40px 40px;
}

@media only screen and (max-width: 1250px){
  .info-box{
    height: 80%;
    font-size: 18px;
  }
  .info-section{
    font-size: 18px;
  }
  .pro-tip{
    font-size: 24px;
  }
  h2{
    font-size: 32px;
  }
 .img-box{
   height: 80%;
 }
 img{
   height: 400px;
   width: 400px;
 }
 .btn{
   height: 60px;
   width: 60px;
   background-size: 30px 30px;
 }
 .btn:hover{
   background-size: 30px 30px;
 }
}
@media only screen and (max-width: 1050px){
  .info-section{
    font-size: 14px;
  }
  .info-box{
    height: 60%;
    font-size: 18px;
  }
  .img-box{
    height: 60%;
  }
}

@media only screen and (max-width: 960px){
  .info-box{
    height: 100%;
    width: 100%;

  }
  .img-box{
    display: none;
  }
  .main-section{
    width: 100%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .text-box{
    height: 70%;
    width: 100%;
    border: 3px solid #0A8845;
    border-left: 0px;
    border-right: 0px;
    border-radius: 0px;
    box-shadow: none;
    font-size: 32px;
  }
  h2{
    font-size: 38px;
  }
  .info-section{
    font-size: 24px;
  }
  .pro-tip{
    font-size: 32px;
  }


}

@media only screen and (max-width: 840px){
  .info-section{
    font-size: 22px;
  }
}
@media only screen and (max-width: 630px){
.info-section{
    font-size: 20px;
  }
  .pro-tip{
    font-size: 26px;
  }
  .text-box{
    height: 50%;
  }
  h1{
    width: 100%;
  }
  .h2{
    margin: 0;
    align-items: center;
    text-align: center;
    font-size: 22px;
    height: 10vh;
  }
  .euro{
    font-size:50px;
  }
  .second-page{
    display: flex;
    justify-content: space-around;
    align-items: center;
    flex-direction: column;
    height: 100vh;
    box-sizing: border-box;
  }
  .compare-box{
    height: 400px;
    width: 300px;
  }
  .compare-box-header h5{
    font-size: 26px;
  }
  .compare-small-box-p{
    font-size: 18px;
  }
  .arrow-box img{
    height: 80px;
    width: 80px;
  }
}
@media only screen and (max-width: 480px){
  .info-section{
    font-size: 16px;
  }
  .pro-tip{
    font-size: 20px;
  }
}
@media only screen and (max-width: 390px){
  .info-section{
    font-size: 14px;
  }
  .pro-tip{
    font-size: 18px;
  }
}
</style>

<template>
<div id="formula-main">
<div class="formula-box">
    <select name="left-select" id="left-select" class="selects" v-model="currencyFrom">
        <option v-for="currency in currencies" :key="currency.id" :value="currency.id">{{ currency.id }}</option>
    </select>
    <input type="number" class="input-number input-left" placeholder="0" onfocus="''" v-model="valueFrom">
    <button id="submit" class="submit" @click="convertMoney()"></button>
    <button id="submit" class="submit2" @click="convertMoney()">SUBMIT</button>
    <input disabled type="number" class="input-number input-right" placeholder="0" v-model="valueTo">
    <select name="rigth-select" id="right-select" class="selects" v-model="currencyTo">
        <option v-for="currency in currencies" :key="currency.id" :value="currency.id">{{ currency.id }}</option>
    </select>
</div>
<h3>tap here to convert</h3>
</div>
</template>

<script>
export default {
    data(){
        return{
            currencies: [],
            currencyFrom: 'EUR',
            currencyTo: 'USD',
            valueFrom: null,
            valueTo: null,
            currencyRate: null,
            todayDate: null,
            currencyHistory: [],
        }
        },
    methods:{
        convert(data){
            this.currencyRate=data[Object.keys(data)[0]];
            this.valueTo=(this.valueFrom*this.currencyRate).toFixed(2);
            this.todayDate=new Date().toISOString().slice(0,10);
            if(this.currencyHistory.length>4){
                this.currencyHistory.shift();
            }
            this.currencyHistory.push({
                id: Math.floor(Math.random() * 100000),
                date: this.todayDate,
                leftCurrency: this.currencyFrom,
                rightCurrency: this.currencyTo,
                currentRate: this.currencyRate,
             });
             this.saveData();
            },
        getCurrencies(){
            fetch('https://free.currconv.com/api/v7/currencies?apiKey=244ce8c22fd118e3b024')
            .then(res => res.json())
            .then(data => this.currencies=data.results)
            .catch(err => console.log(err.message));
        },
        convertMoney(){
            fetch(`https://free.currconv.com/api/v7/convert?q=${this.currencyFrom}_${this.currencyTo}&compact=ultra&apiKey=244ce8c22fd118e3b024`)
            .then(res => res.json())
            .then(data => this.convert(data))
            .catch(err => console.log(err.message));
            this.$emit('newConvert',this.currencyHistory);
        },
        saveData(){
            const parsed = JSON.stringify(this.currencyHistory);
            localStorage.setItem('currencyHistory', parsed);
        },

    },
    mounted(){
        if(!localStorage.getItem('currencyHistory')){
            localStorage.setItem('currencyHistory',this.currencyHistory);
        }else{
            this.currencyHistory=JSON.parse(localStorage.getItem("currencyHistory"));
        }
        this.getCurrencies();
    }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@300;400&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Pacifico&display=swap');
#formula-main{
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    height: 30vh;
}
.formula-box{
    width: 100%;
    height: 150px;
    display: flex;
    justify-content: space-around;
    align-items: center;
}
.selects{
    height: 100px;
    width: 100px;
    background-color: #0A8845;
    font-size: 30px;
    color: white;
    border-radius: 50%;
    border: 0px;
    appearance: none;
    text-align-last: center;
    cursor: pointer;
    font-family: 'Montserrat', sans-serif;
    outline: none;
}
.selects:hover{
    background: #096e38;
    color: #ebebeb;
}
.input-number{
    width: 300px;
    height: 100px;
    border-radius: 30px;
    border: solid 1px grey;
    outline: none;
    font-family: 'Montserrat', sans-serif;
    font-weight: bold;
    color: black;
    font-size: 42px;
    text-align: center;
}
.input-number::-webkit-outer-spin-button,
.input-number::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}
.input-left{
    background-color: white;
    cursor: pointer;
}
.input-left:focus::placeholder{
    color: transparent;
}
.input-right:disabled{
    background: #bef7d9;
    color: grey;
}
.submit{
    width: 100px;
    height: 100px;
    border-radius: 50%;
    cursor: pointer;
    border: none;
    background-image: url(../assets/dollar.svg);
    background-position: center;
    background-repeat: no-repeat;
    background-size: 80px 80px;
    background-color: #BFB904;
}
.submit2{
    width: 100px;
    height: 100px;
    border-radius: 50%;
    cursor: pointer;
    border: none;
    display: none;
    background-color: #BFB904;
}
.submit:hover{
background-image: url(../assets/dollar.svg);
    background-position: center;
    background-repeat: no-repeat;
    background-size: 80px 80px;
    background-color: #b3ad04;
}

h3{
    font-family: 'Montserrat', sans-serif;
    color: #ABABAB;
    font-weight: 100;
    font-size: 18px;

}
@media only screen and (max-width: 1080px){
   .selects{
       width: 80px;
       height: 80px;
        font-size: 26px;
   }
   .input-number{
       width: 250px;
       height: 80px;
       font-size: 36px;
   }
   .submit{
       height: 80px;
       width: 80px;
       background-size: 60px 60px;
   }
   h3{
       font-size: 15px;
   }
   .submit:hover{
       background-size: 60px 60px;
   }

}

@media only screen and (max-width: 840px){
    .selects{
       width: 60px;
       height: 60px;
        font-size: 22px;
   }
   .input-number{
       width: 200px;
       height: 60px;
       font-size: 28px;
   }
   .submit{
       height: 60px;
       width: 60px;
       background-size: 40px 40px;
   }
   .submit:hover{
       background-size: 40px 40px;
   }
   h3{
       font-size: 13px;
   }

}
@media only screen and (max-width: 840px){
    .formula-box{
        display: flex;
        justify-content: space-around;
        align-items: center;
        height: 100%;
    }
    .selects{
        width: 100px;
        height: 40px;
        font-size: 26px;
        border-radius: 20px;
   }
   .input-number{
       width: 120px;
       height: 40px;
       font-size: 28px;
   }

}
@media only screen and (max-width: 630px){
    #formula-main{
        height: 40vh;
        width: 100%;
    }
    .submit{
       display: none;
   }
   .submit2{
       align-items: center;
       justify-content: center;
       display: flex;
        font-family: 'Montserrat', sans-serif;
        color: black;
        font-size: 18px;
        width: 100px;
        height: 30px;
        border-radius: 0px;
        text-align: center;
        font-weight: bold;
   }
   .submit2:hover{
       background-color: #b3ad04;
   }
   .formula-box{
        display: flex;
        justify-content: space-around;
        align-items: center;
        height: 100%;
        flex-direction: column;
        width: 100%;
    }
    h3{
        display: none;
    }
    .input-number{
        font-size: 18px;
    }
}
</style>
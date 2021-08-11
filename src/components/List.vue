<template>
<div id="main-list" >
    <div class="dashboard" >
        <ul class="list">
            <li class="row" @click="openCompareBox(currencyData[n].id)" v-for="(date, n) in currencyData" :key="date.id" >
                <div class="left-box">
                    {{currencyData[n].leftCurrency}} to {{currencyData[n].rightCurrency}} was {{currencyData[n].currentRate}}
                </div>
                <div class="right-box">
                    {{currencyData[n].date}}
                </div>
            </li>
        </ul>
    </div>
</div>
</template>

<script>
export default {
    props: {

    },
    data(){
        return{
            whatColor:'',
            bckColor:'',
            button:'',
            header:'',
            smallBox:'',
            isOpened: false,
            currencyData: [],
            actualCounter:0,
        }
    },

methods:{
    openCompareBox(n){
        this.$emit('isOpened',this.isOpened);
        this.$emit('whichId',n);
    },
    closeCompareBox(){
        this.bckColor=document.querySelector('.compare-box');
        this.bckColor.style.display="none";
    },
    themeDark(){
        this.bckColor.style.background="#363a45";
        this.smallBox.forEach(element => {element.style.color="white";});
        this.button.style.color="white";
        this.header.style.color="white";
    },
    themeLigth(){
        this.bckColor.style.background="white";
        this.smallBox.forEach(element => {element.style.color="black";});
        this.button.style.color="black";
        this.header.style.color="black";
    },
},
mounted(){
    if(localStorage.getItem("currencyHistory")){
        this.currencyData=JSON.parse(localStorage.getItem("currencyHistory"));
    }
},

}

</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@300;400&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Pacifico&display=swap');
.dark-mode{
    color: white;
    background:#363a45;
}

#main-list{
    width: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 60vh;
}
.dashboard{
    width: 1000px;
    height: 100%;
    border: 2px solid;
    height: 450px;
    color: #0A8845;
    border-top-left-radius: 30px;
    border-top-right-radius: 30px;
    border-bottom: none;
    display: flex;
    justify-content: space-between;
    align-items: center;
    flex-direction: column;
    font-family: 'Montserrat', sans-serif;
}
.row{
    width: 100%;
    display: flex;
    justify-content: space-around;
    align-items: center;
    height: 80px;
    border-bottom: 1px solid grey;
}
.list{
    height: 100%;
    width: 100%;
}
.left-box{
    width: 40%;
    font-weight: bold;
    font-size: 22px;
    text-align: center;
}
.right-box{
    width: 40%;
    color: #BFB904;
    font-size: 22px;
    text-align: center;
}
.row:hover{
    color: #0A8845;
    cursor: pointer;
    border-bottom: 2px solid #0A8845;
}

@media only screen and (max-width: 1080px){
    .dashboard{
        width: 800px;
    }
    .left-box, .right-box{
        font-size: 18px;
        text-align: center;
    }
}
@media only screen and (max-width: 850px){
    .dashboard{
        width: 600px;
    }
    .left-box, .right-box{
        text-align: center;
        font-size: 16px;
    }
}
@media only screen and (max-width: 630px){
    .dashboard{
        width: 100%;
        border-radius: 0px;
        border-left: 0px;
        border-right: 0px;
        height: 100%;
    }
    #main-list{
        height: 50vh;
    }
     .left-box, .right-box{
        text-align: center;
        font-size: 16px;
    }
    .row{
        height: 70px;
    }
}

</style>
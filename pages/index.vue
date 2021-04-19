<template>
  <div class="container">
    <div>
      <input type="range" id="nbCreators" name="nbCreators" min="0" max="1000" v-model="nbCreators" @change="calculate">
      <label for="nbCreators">Number of creators: <input type="text" v-model="nbCreators"></label>
    </div>

    <div>
      <input type="range" id="userPerCreator" name="userPerCreator" min="0" max="1000" v-model="userPerCreator"  @change="calculate">
      <label for="userPerCreator">Number of users per creator: <input type="text" v-model="userPerCreator"></label>
    </div>

    <div>
      <input type="range" id="percentageCreatorKeepsPerUser" name="percentageCreatorKeepsPerUser" min="0" max="1" step="0.05" v-model="percentageCreatorKeepsPerUser"  @change="calculate">
      <label for="percentageCreatorKeepsPerUser">percentage Creator Keeps Per User: <input type="text" v-model="percentageCreatorKeepsPerUser">{{percentageCreatorKeepsPerUser*100}}% // Only first month</label>
    </div>

    <div>
      <input type="range" id="PercentageForLife" name="PercentageForLife" min="0" max="1" step="0.05" v-model="PercentageForLife"  @change="calculate">
      <label for="PercentageForLife">percentage Creator Keeps Per User for life: <input type="text" v-model="PercentageForLife">{{PercentageForLife*100}}% // only AFTER first month</label>
    </div>

    <div>
      <input type="range" id="subPrice" name="subPrice" min="0" max="20" v-model="subPrice" step="0.5" @change="calculate">
      <label for="subPrice">Subscription price: <input type="text" v-model="subPrice">$</label>
    </div>

    <div>
      <input type="range" id="peachCommission" name="peachCommission" min="0" max="1" step="0.05" v-model="peachCommission"  @change="calculate">
      <label for="peachCommission">Peach commission on sub price: <input type="text" v-model="peachCommission"> {{peachCommission*100}}% // comission prise des le debut sur le sub price</label>
    </div>

    <div>
      <input type="range" id="VideosPerUser" name="VideosPerUser" min="0" max="300" v-model="VideosPerUser"  @change="calculate">
      <label for="VideosPerUser">Average number of videos views per user: <input type="text" v-model="VideosPerUser"></label>
    </div>

    <div>
      <input type="range" id="RPV" name="RPV" min="0" max="0.1" step="0.005" v-model="RPV"  @change="calculate">
      <label for="RPV">Rate per view:<input type="text" v-model="RPV"> $</label>
    </div>

    <div>
      <input type="range" id="peachPercent" name="peachPercent" min="0" max="1" step="0.05" v-model="peachPercent"  @change="calculate">
      <label for="peachPercent">Peach percentage on remaning money:<input type="text" v-model="peachPercent"> {{peachPercent*100}}% // comission prise sur l'argent qui reste, pour le processing</label>
    </div>

    <div>
      <input type="range" id="creatorPercent" name="creatorPercent" min="0" max="1" step="0.05" v-model="creatorPercent"  @change="calculate">
      <label for="creatorPercent">Creator percentage on remaning money:<input type="text" v-model="creatorPercent"> {{creatorPercent*100}}% </label>
    </div>

<!--    <div>-->
<!--      <input type="range" id="userLoss" name="userLoss" min="0" max="1" step="0.05" v-model="userLoss"  @change="calculate">-->
<!--      <label for="userLoss">User loss per month:<input type="text" v-model="userLoss"> {{userLoss*100}}%</label>-->
<!--    </div>-->
    <br/>

    <h1>Total Money generated: {{parseInt(moneyGenerated)}}$</h1>
    <h1>Total Used: {{parseInt(PeachResult + (creatorResult*nbCreators))}}$</h1>

<br/>
    <h1>Peach result: <span :class="[{'green': PeachResult > 0}, {'red': PeachResult < 0}]">{{parseInt(PeachResult)}}$</span></h1>
    <h1>Creator result: <span :class="[{'green': creatorResult > 0}, {'red': creatorResult < 0}]">{{parseInt(creatorResult)}}$</span></h1>

    <div class="btn-container">
      <button class="btn" @click="calculate">Calculate</button>
      <button class="month" @click="changeMonth">Month {{month}}</button>
    </div>


  </div>
</template>

<script>

export default {
  data(){
    return{
      nbCreators: 10,
       userPerCreator: 350,
       percentageCreatorKeepsPerUser: 1,
       peachCommission: 0.2 ,
       PercentageForLife: 0.25,
       subPrice: 9.99,
       VideosPerUser: 64,
       RPV: 0.035,
       peachPercent: 0.2 ,
       creatorPercent: 0.8 ,
       userLoss: 1,
      moneyGenerated: 0,
      totalPricePerViews: 0,
      TotalLifeTimeRevenuShare: 0,

      PeachResult: 0,
      creatorResult:0,
      PeachLoss:0,
      month: 1,
    }
  },
 methods:{
   calculate(){
     this.PeachResult= 0
     this.creatorResult=0
     this.PeachLoss=0
     this.TotalLifeTimeRevenuShare =0

     let PeachLoss = 0
     let PeachEarnings;
     let eachCreatorMake=0;

     let users = this.nbCreators*this.userPerCreator

     this.moneyGenerated = users * this.subPrice; // total money generated
     console.log('moneyGenerated', this.moneyGenerated)

     if(this.month === 1){
       eachCreatorMake = this.moneyGenerated / this.nbCreators*this.percentageCreatorKeepsPerUser //
       console.log('eachCreatorMake', eachCreatorMake)

        PeachEarnings = (this.moneyGenerated - eachCreatorMake*this.nbCreators) * this.peachCommission
       console.log('PeachEarnings', PeachEarnings)
     }else{
        PeachEarnings = this.moneyGenerated * this.peachCommission
       console.log('PeachEarnings', PeachEarnings)
     }


     if(this.month === 2)
      this.TotalLifeTimeRevenuShare = this.subPrice*this.PercentageForLife*users
     console.log('TotalLifeTimeRevenuShare', this.TotalLifeTimeRevenuShare)

     this.totalPricePerViews = users*this.VideosPerUser*this.RPV
     console.log('totalPricePerViews', this.totalPricePerViews)

     let remaningMoney = this.moneyGenerated - PeachEarnings - this.totalPricePerViews - this.TotalLifeTimeRevenuShare - (eachCreatorMake*this.nbCreators)
     console.log('remaningMoney', remaningMoney)
     if(remaningMoney < 0) {
       this.PeachLoss = remaningMoney
       remaningMoney = 0
     }

     let creatorSplit = remaningMoney*this.creatorPercent
     console.log('creatorSplit', creatorSplit)

     let peachSplit = remaningMoney*this.peachPercent
     console.log('peachSplit', peachSplit)


     this.PeachResult = (PeachEarnings + peachSplit) + this.PeachLoss
     this.creatorResult = (this.totalPricePerViews/this.nbCreators) + (creatorSplit/this.nbCreators) + (this.TotalLifeTimeRevenuShare/this.nbCreators) + eachCreatorMake
   },
   changeMonth(){
     if(this.month === 1)
       this.month = 2
     else this.month = 1
     this.calculate()
   }
 },
}
</script>

<style>
  .container {
    padding: 5% 10%;
  }
.red {
  color: red;
}

.green {
  color: green;
}

.btn-container {
  display: flex;
  justify-content: center;
  width: 100%;
}

  .btn{
    background-color: green;
    color: white;
    font-weight: bold;
    font-size: 18px;
    padding: 15px 10px;
    border-radius: 7px;
    border: none;
    cursor: pointer;
    margin: 0 20px;
  }


.month{
  background-color: red;
  color: white;
  font-weight: bold;
  font-size: 18px;
  padding: 15px 10px;
  border-radius: 7px;
  border: none;
  cursor: pointer;
  margin: 0 20px;
}
</style>

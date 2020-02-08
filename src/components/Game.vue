<template>
  <div class="random col-8 m-auto">
    <b-card>
    <div class="container mb-5" style="min-height: 450px">
      <div class="players text-center d-flex justify-content-between">
        <!-- player one -->
        <div class="player">
          <img src="../assets/player1.gif">
            <h4 class="player-name">{{ player.name }}</h4>
            <div class="health-bar">
                <p class="healthRemaining">
                {{ player.hp }}
              </p>
              <div class="health-left" :style="{width: player.hp + '%'}">
                <div class="health-empty">
                  
                </div>
              </div>
            </div>
        </div>
        <!-- player PC -->
          <div class="player">
            <img src="../assets/monster2.gif">
            <h4 class="player-name">{{ computer.name }}</h4>
            <div class="health-bar">
              <p class="healthRemaining">
                {{ computer.hp }}
              </p>
              <div class="health-left" :style="{width: computer.hp + '%'}">
                <div class="health-empty">
                  
                </div>
              </div>
            </div>
        </div>
      </div>
    </div>
    </b-card>

    <b-card class="text-center mt-2">
      <b-button v-if="!isGameStarted" @click="reset">Start</b-button>
      <div v-else>
      <b-button variant="danger" @click="run('attack')" >Attack</b-button>
      <b-button variant="warning" @click="run('s-attack')">StrenghtAttack</b-button>
      <b-button variant="btn btn-success" @click="run('heal')">Heal Yourself</b-button>
      <b-button variant="dark" @click="reset">Run away</b-button>
      </div>
    </b-card>

    <b-card v-if="attacks.length > 1" class="mt-3">
        <div class="text-center" v-for="attack in attacks" :key="attack">
            <div class="mt-3" v-html="attack"></div>
        </div>
    </b-card>
</div> 
</template>

<script>
export default {
  name: 'Game',
  props: {
    msg: String
  },
  data() {
    return {

      player : {
        name: "You",
        hp: 100,
        att: 
        {
          min: 1,
          max: 10
        },
        satt: 
        {
          min: 7,
          max: 20
        },
        heal: 
        {
          min :5,
          max : 20
        },
      },
      computer: {
        name: "Opponent",
        hp: 100,
        att: 
        {
          min : 5,
          max : 15
        },
      },

      isGameStarted: false,
      attacks: []
    }
  },
  methods:{
    run(action)
    {
      this.attack(this.player,action)
      this.attack(this.computer,"attack")
    },
    
    attack(player,action){
      let power;
      if(action == "heal"){
        power = Math.floor(Math.random() * player.heal.max) + player.heal.min;
      }else if(action == "s-attack"){
        power = Math.floor(Math.random() * player.satt.max) + player.satt.min;
      }else{
        power = Math.floor(Math.random() * player.att.max) + player.att.min;
      }

      this.attacks.push(this.showHtmlWithAttacks(player,action,power));

          if(player != this.computer)
          {
            if(action == "heal")
            {
              if(this.player.hp < 100)
              {
                this.player.hp += power;
              }
            }else{
              this.computer.hp -= power;
            }
          }else{
            this.player.hp -= power;
          }
    },

    

    showHtmlWithAttacks(from,action,power)
    {
      this.checkIfZerro();

      let actionClass = "";
      let to = this.player.name;

      if(action == "attack" && from.name == this.player.name)
      {
        actionClass = "bg-warning";
        to = this.computer.name;
      }
      else if(action =="heal" && from.name == this.player.name){
        return `<p class="bg-success block-size"> ${from.name} get ${power} HP</p>`;
      }
      else if(action == "s-attack" && from.name == this.player.name)
      {
        to = this.computer.name;
        actionClass = "bg-primary";
      }else{
        actionClass = "bg-danger"
      }
      
      return `<div class="${actionClass} block-size"> ${from.name} attacks to ${to} with ${power} power</div>`;
    },
    checkIfZerro()
    {
        if(this.player.hp <= 0)
        {
          this.player.hp = 0;
          this.playAgain();
          return false;
        }

        if(this.computer.hp <= 0)
        {
          this.computer.hp = 0;
          this.playAgain();
        }
    },
    reset()
    {
      this.isGameStarted = !this.isGameStarted;
      this.computer.hp = 100;
      this.player.hp = 100;
      this.attacks = [];
    },
    playAgain()
    {
      let a = confirm("Play again?");
      if(a)
      {
        this.reset();
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.player {
  margin-left:60px;
  width: 50%;
  height: 40px;
}

.block-size {
  width: 100%;
  padding-top: 10px;
}
.health-bar{
  background-color: blanchedalmond;
  width : 90%;
  height: 40px;
  position:relative;
}

.health-left {
  background-color: green;
  height: 40px;
}
.health-empty{
  background-color: transparent;
}

.healthRemaining {
  top: 50%;
  left: 50%;
  transform: translate(-50%,-50%);
  position:absolute;
}

</style>

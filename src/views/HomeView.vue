<template>
  <div class="home-view">
    <div class="top">
      <RaceSection class="race-section" :raceStates="raceStates" />
    </div>
    <div class="bottom">
      <div class="left">
        <AudienceSection
          class="audience-section"
          :playerStates="playerStates"
        />
      </div>
      <div class="middle">
        <BetSection class="bet-section" :bet="bet" />
        <div
          class="initial-section"
          v-if="globalState === 'initial' || globalState === 'wait'"
        >
          <el-button
            type="danger"
            @click="skipInitialize"
            v-if="globalState === 'initial' || globalState === 'wait'"
            >跳过初始化</el-button
          >
          <el-button
            type="primary"
            @click="initialize"
            v-if="globalState === 'initial'"
            >初始化</el-button
          >
          <el-button
            type="warning"
            @click="reInitialize"
            v-if="globalState === 'wait'"
            >重新初始化</el-button
          >
          <el-button
            type="primary"
            @click="confirmInitialize"
            v-if="globalState === 'wait'"
            >继续</el-button
          >
        </div>
        <div class="choose-section" v-if="globalState === 'running'">
          <el-radio-group class="choose" v-model="choose">
            <el-radio class="choose-item" label="1" border>
              <div class="dice">骰子</div>
            </el-radio>
            <el-radio
              class="choose-item"
              label="2"
              border
              :disabled="forecast_disabled"
            >
              <div class="forecast">
                预测
                <div class="choose-item-info">
                  预测1:
                  <el-radio-group
                    v-model="singleForecast.type"
                    :disabled="forecast_disabled || choose != '2'"
                  >
                    <el-radio label="win">冠军</el-radio>
                    <el-radio label="lose">垫底</el-radio>
                  </el-radio-group>
                </div>
                <div class="choose-item-info">
                  预测2:
                  <el-radio-group
                    v-model="singleForecast.camelId"
                    :disabled="forecast_disabled || choose != '2'"
                  >
                    <el-radio
                      v-for="(camel, index) in camels.slice(0, 5)"
                      :key="index"
                      :label="index.toString()"
                      :disabled="
                        playerStates.length > 0
                          ? playerStates[currentPlayer].forecast.includes(index)
                          : false
                      "
                      >{{ camel }}</el-radio
                    >
                  </el-radio-group>
                </div>
              </div>
            </el-radio>
            <el-radio
              class="choose-item"
              label="3"
              border
              :disabled="bet_disabled"
            >
              <div class="bet">
                下注
                <div class="choose-item-info">
                  下注1:
                  <el-radio-group
                    v-model="singleBet.camelId"
                    :disabled="bet_disabled || choose != '3'"
                  >
                    <el-radio
                      v-for="(camel, index) in camels.slice(0, 5)"
                      :key="index"
                      :label="index.toString()"
                      :disabled="
                        bet.length > 0 ? bet[index].players.length >= 4 : false
                      "
                      >{{ camel }}</el-radio
                    >
                  </el-radio-group>
                </div>
              </div>
            </el-radio>
            <el-radio
              class="choose-item"
              label="4"
              border
              :disabled="trap_disabled"
            >
              <div class="trap">
                陷阱
                <div class="choose-item-info">
                  陷阱1:
                  <el-select
                    v-model="singleTrap.cellId"
                    placeholder="请选位置"
                    size="small"
                    :disabled="trap_disabled || choose != '4'"
                  >
                    <el-option
                      v-for="item in Array.from(
                        { length: this.raceMileage },
                        (_, index) => index + 1
                      )"
                      :key="item"
                      :label="item"
                      :value="item"
                    />
                  </el-select>
                </div>
                <div class="choose-item-info">
                  陷阱2:
                  <el-radio-group
                    v-model="singleTrap.type"
                    :disabled="trap_disabled || choose != '4'"
                  >
                    <el-radio label="+1">+1</el-radio>
                    <el-radio label="-1">-1</el-radio>
                  </el-radio-group>
                </div>
              </div>
            </el-radio>
          </el-radio-group>
          <el-button-group class="operate">
            <div class="current-player">{{ playerNames[currentPlayer] }}</div>
            <el-button type="danger" @click="reset">重置</el-button>
            <el-button
              type="primary"
              @click="submit"
              :disabled="submit_disabled"
              >提交</el-button
            >
          </el-button-group>
        </div>
        <div class="phased-section" v-if="globalState === 'settling'">
          <el-button type="primary" @click="comeon">继续</el-button>
        </div>
        <div class="finally-section" v-if="globalState === 'finished'">
          <el-button type="danger" @click="unveil">结束</el-button>
        </div>
      </div>
      <div class="right">
        <ForecastSection class="forecast-section" :forecast="forecast" />
        <DiceSection class="dice-section" :diceStates="diceStates" />
      </div>
    </div>
  </div>
</template>
  
<script>
import AudienceSection from "@/components/AudienceSection";
import BetSection from "@/components/BetSection";
import DiceSection from "@/components/DiceSection";
import ForecastSection from "@/components/ForecastSection";
import RaceSection from "@/components/RaceSection";
export default {
  name: "HomeView",
  components: {
    AudienceSection,
    BetSection,
    DiceSection,
    ForecastSection,
    RaceSection,
  },
  data() {
    return {
      globalState: "initial", //info,initial,wait,running,settling,finished
      playerNum: 8,
      currentPlayer: 0,
      playerNames: [
        "Player 1",
        "Player 2",
        "Player 3",
        "Player 4",
        "Player 5",
        "Player 6",
        "Player 7",
        "Player 8",
      ],
      playerStates: [],
      raceMileage: 16,
      raceStates: [],
      camels: ["red", "orange", "blue", "cyan", "purple", "black", "white"],
      camelStates: [],
      forecast: [],
      bet: [],
      trap: [],
      dices: [],
      diceStates: [],

      choose: "0",
      singleForecast: {
        type: "",
        playerId: "",
        camelId: "",
      },
      singleBet: {
        playerId: "",
        camelId: "",
      },
      singleTrap: {
        type: "",
        playerId: "",
        cellId: "",
      },
    };
  },
  computed: {
    forecast_disabled() {
      return this.playerStates.length > 0
        ? this.playerStates[this.currentPlayer].forecast.length === 5
        : false;
    },
    bet_disabled() {
      if (this.bet.length === 0) return false;
      return this.bet.reduce(
        (pre, cur) => pre && cur.players.length >= 4,
        true
      );
    },
    trap_disabled() {
      let players = [];
      this.trap.forEach((item) => {
        players.push(item.playerId);
      });
      return (
        this.trap.length >= this.playerNum ||
        players.includes(this.currentPlayer)
      );
    },
    submit_disabled() {
      if (this.choose === "0") return true;
      else if (this.choose === "1") return this.dices.length === 0;
      else if (this.choose === "2")
        return (
          this.singleForecast.type === "" || this.singleForecast.camelId === ""
        );
      else if (this.choose === "3") return this.singleBet.camelId === "";
      else if (this.choose === "4")
        return this.singleTrap.cellId === "" || this.singleTrap.type === "";
      else return false;
    },
  },
  mounted() {
    this.playerNames.slice(0, this.playerNum).forEach((playerName, index) => {
      this.playerStates.push({
        id: index,
        name: playerName,
        money: 3,
        forecast: [],
        lotteries: 0,
      });
    });
    for (let i = 0; i < this.raceMileage; i++) {
      this.raceStates.push({
        id: i,
        camels: [],
        traps: [],
      });
    }
    this.camels.forEach((camel, index) => {
      if (camel === "black" || camel === "white") {
        this.camelStates.push({
          id: index,
          name: camel,
          position: this.raceMileage,
          direction: -1,
        });
      } else {
        this.camelStates.push({
          id: index,
          name: camel,
          position: -1,
          direction: 1,
        });
      }
    });
    this.camels.slice(0, 5).forEach((camel) => {
      this.bet.push({ camel: camel, players: [] });
    });
    for (let i = 0; i < 6; i++) {
      this.dices.push(i);
    }
  },
  methods: {
    skipInitialize() {
      this.camelStates.forEach((camelState) => {
        if (camelState.name === "black" || camelState.name === "white") {
          camelState.position = this.raceMileage;
        } else {
          camelState.position = -1;
        }
      });
      for (let i = 0; i < this.raceMileage; i++) {
        this.raceStates[i].camels = [];
      }
      this.globalState = "running";
    },
    initialize() {
      let diceArr = [0, 1, 2, 3, 4, 5, 6];
      for (let i = 0; i < 7; i++) {
        let index = Math.floor(Math.random() * diceArr.length);
        let step = Math.floor(Math.random() * 3) + 1;
        let id = diceArr[index];
        diceArr.splice(index, 1);
        this.camelRace(id, step);
      }
      this.globalState = "wait";
    },
    reInitialize() {
      this.skipInitialize();
      this.initialize();
      this.globalState = "wait";
    },
    confirmInitialize() {
      this.globalState = "running";
    },
    reset() {
      this.choose = "0";
      this.singleForecast = {
        type: "",
        playerId: "",
        camelId: "",
      };
      this.singleBet = {
        playerId: "",
        camelId: "",
      };
    },
    submit() {
      if (this.choose === "1") {
        this.rollDice();
      } else if (this.choose === "2") {
        this.makeForecast();
      } else if (this.choose === "3") {
        this.makeBet();
      } else if (this.choose === "4") {
        this.setTrap();
      }
      this.reset();
      this.currentPlayer = (this.currentPlayer + 1) % this.playerNum;

      if (this.globalState === "settling") {
        console.log("阶段结算环节");
        // 奖券结算
        this.lotterySettlement();
        // 轮注结算
        this.betSettlement();
      } else if (this.globalState === "finished") {
        console.log("游戏结束");
        // 奖券结算
        this.lotterySettlement();
        // 轮注结算
        this.betSettlement();
        // 结果结算
        this.forecastSettlement();
      }
    },
    comeon() {
      this.dices = [];
      this.diceStates = [];
      for (let i = 0; i < 6; i++) {
        this.dices.push(i);
      }
      this.bet.forEach((item) => {
        item.players = [];
      });
      this.globalState = "running";
    },
    unveil() {
      this.globalState = "info";
    },
    rank() {
      let ranking = [];
      this.camelStates.slice(0, 5).forEach((camelState, index) => {
        ranking.push({
          id: index,
          position: camelState.position,
        });
      });
      ranking.sort((a, b) => {
        if (a.position === b.position) {
          let pos = a.position;
          if (pos === -1) return 0;
          let a_index = this.raceStates[pos].camels.indexOf(a.id);
          let b_index = this.raceStates[pos].camels.indexOf(b.id);
          return b_index - a_index;
        } else return b.position - a.position;
      });
      return ranking;
    },
    lotterySettlement() {
      this.playerStates.forEach((playerState) => {
        playerState.money += playerState.lotteries;
        playerState.lotteries = 0;
      });
    },
    betSettlement() {
      let ranking = this.rank();
      let [first, second] = ranking.slice(0, 2);
      this.bet.forEach((item, index) => {
        if (index == first.id) {
          item.players.forEach((playerId, rank) => {
            if (rank == 0) this.playerStates[playerId].money += 5;
            else if (rank == 1) this.playerStates[playerId].money += 3;
            else if (rank == 2) this.playerStates[playerId].money += 2;
            else if (rank == 3) this.playerStates[playerId].money += 2;
          });
        } else if (index == second.id) {
          item.players.forEach((playerId) => {
            this.playerStates[playerId].money += 1;
          });
        } else {
          item.players.forEach((playerId) => {
            this.playerStates[playerId].money -= 1;
          });
        }
      });
    },
    forecastSettlement() {
      let ranking = this.rank();
      let [first, last] = [ranking[0], ranking[ranking.length - 1]];
      let win = [],
        lose = [],
        error = [];
      this.forecast.forEach((item) => {
        if (item.type === "win" && item.camelId === first.id)
          win.push(item.playerId);
        else if (item.type === "lose" && item.camelId === last.id)
          lose.push(item.playerId);
        else error.push(item.playerId);
      });
      for (let i = 0; i < win.length; i++) {
        if (i === 0) this.playerStates[win[i]].money += 8;
        else if (i === 1) this.playerStates[win[i]].money += 5;
        else if (i === 2) this.playerStates[win[i]].money += 3;
        else if (i === 3) this.playerStates[win[i]].money += 2;
        else if (i === 4) this.playerStates[win[i]].money += 1;
      }
      for (let i = 0; i < lose.length; i++) {
        if (i === 0) this.playerStates[lose[i]].money += 8;
        else if (i === 1) this.playerStates[lose[i]].money += 5;
        else if (i === 2) this.playerStates[lose[i]].money += 3;
        else if (i === 3) this.playerStates[lose[i]].money += 2;
        else if (i === 4) this.playerStates[lose[i]].money += 1;
      }
      for (let i = 0; i < error.length; i++) {
        this.playerStates[error[i]].money -= 1;
      }
    },
    camelRace(id, step) {
      if (id === 5 || id === 6) {
        if (this.camelStates[id].position === this.raceMileage) {
          this.camelStates[id].position +=
            step * this.camelStates[id].direction;
          this.raceStates[this.camelStates[id].position].camels.push(id);
        } else {
          let index =
            this.raceStates[this.camelStates[id].position].camels.indexOf(id);
          let arr =
            this.raceStates[this.camelStates[id].position].camels.splice(index);
          for (let i = 0; i < arr.length; i++) {
            this.camelStates[arr[i]].position +=
              step * this.camelStates[id].direction;
          }
          if (this.camelStates[id].position < 0) {
            for (let i = 0; i < arr.length; i++) {
              this.camelStates[arr[i]].position = 0;
            }
            // return;
          }
          this.raceStates[this.camelStates[id].position].camels.push(...arr);
        }
      } else {
        if (this.camelStates[id].position === -1) {
          this.camelStates[id].position +=
            step * this.camelStates[id].direction;
          this.raceStates[this.camelStates[id].position].camels.push(id);
        } else {
          let index =
            this.raceStates[this.camelStates[id].position].camels.indexOf(id);
          let arr =
            this.raceStates[this.camelStates[id].position].camels.splice(index);
          for (let i = 0; i < arr.length; i++) {
            this.camelStates[arr[i]].position +=
              step * this.camelStates[id].direction;
          }
          if (this.camelStates[id].position >= this.raceMileage) {
            for (let i = 0; i < arr.length; i++) {
              this.camelStates[arr[i]].position = this.raceMileage - 1;
            }
            this.globalState = "finished";
            // return;
          }
          this.raceStates[this.camelStates[id].position].camels.push(...arr);
        }
      }
    },
    rollDice() {
      if (this.dices.length === 1) return;

      this.playerStates[this.currentPlayer].lotteries += 1;

      let index = Math.floor(Math.random() * this.dices.length);
      let step = Math.floor(Math.random() * 3) + 1;
      let id =
        this.dices[index] === 5
          ? this.dices[index] + Math.floor(Math.random() * 2)
          : this.dices[index];
      this.diceStates.push({
        id: id,
        playerId: this.currentPlayer,
        color: this.camels[this.dices[index]],
        step: step,
      });
      this.dices.splice(index, 1);
      this.camelRace(id, step);
      if (this.dices.length === 1) this.globalState = "settling";
    },
    makeForecast() {
      if (
        this.playerStates[this.currentPlayer].forecast.includes(
          Number(this.singleForecast.camelId)
        )
      )
        return;
      this.playerStates[this.currentPlayer].forecast.push(
        Number(this.singleForecast.camelId)
      );
      this.forecast.push({
        type: this.singleForecast.type,
        playerId: this.currentPlayer,
        camelId: Number(this.singleForecast.camelId),
      });
    },
    makeBet() {
      if (this.bet[Number(this.singleBet.camelId)].players.length >= 4) return;
      this.bet[Number(this.singleBet.camelId)].players.push(this.currentPlayer);
    },
    setTrap() {
      this.trap.push({
        type: Number(this.singleTrap.type),
        playerId: this.currentPlayer,
        cellId: Number(this.singleTrap.cellId),
      });
    },
  },
};
</script>
  
<style scoped>
.home-view {
  height: 100%;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
  background-color: #eedfbb;
}

.top {
  height: 20%;
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
  align-items: center;
}

.bottom {
  height: 80%;
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
  align-items: center;
}

.left {
  height: 100%;
  width: 20%;
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
  align-items: center;
}

.middle {
  height: 100%;
  width: 60%;
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
  align-items: center;
}

.right {
  height: 100%;
  width: 20%;
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
  align-items: center;
}

.bet-section {
  height: 50%;
  width: 100%;
}

.initial-section {
  height: 50%;
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
  align-items: center;
}

.choose-section {
  height: 50%;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
  align-items: center;
}

.choose-section .choose {
  height: 80%;
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
  align-items: center;
  flex-wrap: nowrap;
  border: 1px solid black;
}

.choose-section .choose-item {
  height: 100%;
  width: 25%;
  display: flex;
  flex-direction: row;
  border: 1px solid black;
}

.choose-section .operate {
  height: 20%;
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
  align-items: center;
  border: 1px solid black;
}

.phased-section {
  height: 50%;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
  align-items: center;
}

.finally-section {
  height: 50%;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
  align-items: center;
}

.forecast-section {
  height: 80%;
  width: 100%;
}

.dice-section {
  height: 20%;
  width: 100%;
}
</style>
  
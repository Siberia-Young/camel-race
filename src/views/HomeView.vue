<template>
  <div class="home-view">
    <div class="info-section" v-if="this.globalState === 'info'">
      <div class="info-show">
        <el-text class="game-title" size="large">骆驼大赛</el-text>
        <div class="info-input">
          <div class="player-num">
            <el-text size="large">请选择人数：</el-text>
            <el-select v-model="playerNum" placeholder="请选人数" size="large">
              <el-option
                v-for="item in Array.from(
                  { length: 7 },
                  (_, index) => index + 2
                )"
                :key="item"
                :label="Number(item)"
                :value="item"
              />
            </el-select>
          </div>
          <el-radio-group class="player-name" v-model="customizeOrNot">
            <div><el-text>请设置玩家昵称：</el-text></div>
            <el-radio class="custom-radio" :label="true" border>
              <el-text size="large">自定义：</el-text>
            </el-radio>
            <el-input
              type="textarea"
              :autosize="{ minRows: 1, maxRows: 8 }"
              :rows="3"
              resize="none"
              :placeholder="'请输入' + playerNum + '个玩家昵称（用“、”隔开）'"
              v-model="customizePlayerNamesStr"
              :disabled="!customizeOrNot"
            >
            </el-input>
            <div class="el-text-box">
              <el-text type="warning" v-show="repeat_prompt">
                自定义昵称中有重复的，请修改！
              </el-text>
            </div>
            <el-radio class="custom-radio" :label="false" border>
              <el-text size="large">默认</el-text>
            </el-radio>
            <div class="el-text-box">
              <el-text type="info"> 默认昵称为：玩家1、玩家2、玩家3…… </el-text>
            </div>
          </el-radio-group>
        </div>
        <div class="next-box">
          <el-button type="primary" round @click="next" :disabled="next_able">
            继续
          </el-button>
        </div>
      </div>
      <div class="rules">
        <el-text class="rules-title">桌游规则</el-text>
      </div>
    </div>
    <div class="top">
      <RaceSection
        class="race-section"
        :raceStates="raceStates"
        :camels="camels"
        :colors="colors"
      />
    </div>
    <div class="bottom">
      <div class="left">
        <AudienceSection
          class="audience-section"
          :playerStates="playerStates"
          :playerNames="playerNames"
          :currentPlayer="currentPlayer"
        />
      </div>
      <div class="middle">
        <BetSection class="bet-section" :bet="bet" />
        <div
          class="control-section initial-section"
          v-if="globalState === 'initial' || globalState === 'wait'"
        >
          <el-button
            type="danger"
            @click="skipInitialize"
            v-if="globalState === 'initial' || globalState === 'wait'"
            >跳过初始化</el-button
          >
          <el-button
            :type="globalState === 'initial' ? 'primary' : 'warning'"
            @click="initialize"
            >{{
              globalState === "initial" ? "初始化" : "重新初始化"
            }}</el-button
          >
          <el-button
            type="primary"
            @click="confirmInitialize"
            v-if="globalState === 'wait'"
          >
            继续
          </el-button>
        </div>
        <div
          class="control-section choose-section"
          v-if="globalState === 'running'"
        >
          <el-radio-group class="choose" v-model="choose">
            <div class="choose-item">
              <div class="choose-title"><el-text>骰子</el-text></div>
              <el-radio label="1" border>
                <div class="diec">
                  <div class="choose-item-info">
                    <div class="choose-item-info-title">骰子</div>
                  </div>
                </div>
              </el-radio>
            </div>
            <div class="choose-item">
              <div class="choose-title"><el-text>预测</el-text></div>
              <el-radio
                class="choose-item"
                label="2"
                border
                :disabled="forecast_disabled"
              >
                <div class="forecast">
                  <div class="choose-item-info">
                    <div class="choose-item-info-title">预测类型:</div>
                    <el-radio-group
                      v-model="singleForecast.type"
                      :disabled="forecast_disabled || choose != '2'"
                    >
                      <el-radio-button label="win">冠军</el-radio-button>
                      <el-radio-button label="lose">垫底</el-radio-button>
                    </el-radio-group>
                  </div>
                  <div class="choose-item-info">
                    <div class="choose-item-info-title">骆驼颜色:</div>
                    <el-radio-group
                      v-model="singleForecast.camelId"
                      :disabled="forecast_disabled || choose != '2'"
                    >
                      <el-radio-button
                        v-for="(camel, index) in camels.slice(0, 5)"
                        :key="index"
                        :label="index.toString()"
                        :disabled="
                          playerStates.length > 0
                            ? playerStates[currentPlayer].forecast.includes(
                                index
                              )
                            : false
                        "
                      >
                        {{ camel }}
                      </el-radio-button>
                    </el-radio-group>
                  </div>
                </div>
              </el-radio>
            </div>
            <div class="choose-item">
              <div class="choose-title"><el-text>下注</el-text></div>
              <el-radio
                class="choose-item"
                label="3"
                border
                :disabled="bet_disabled"
              >
                <div class="bet">
                  <div class="choose-item-info">
                    <div class="choose-item-info-title">骆驼颜色:</div>
                    <el-radio-group
                      v-model="singleBet.camelId"
                      :disabled="bet_disabled || choose != '3'"
                    >
                      <el-radio-button
                        v-for="(camel, index) in camels.slice(0, 5)"
                        :key="index"
                        :label="index.toString()"
                        :disabled="
                          bet.length > 0
                            ? bet[index].players.length >= 4
                            : false
                        "
                      >
                        {{ camel }}
                      </el-radio-button>
                    </el-radio-group>
                  </div>
                </div>
              </el-radio>
            </div>
            <div class="choose-item">
              <div class="choose-title"><el-text>陷阱</el-text></div>
              <el-radio
                class="choose-item"
                label="4"
                border
                :disabled="trap_disabled"
              >
                <div class="trap">
                  <div class="choose-item-info">
                    <div class="choose-item-info-title">陷阱位置:</div>
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
                    <div class="choose-item-info-title">陷阱类型:</div>
                    <el-radio-group
                      v-model="singleTrap.type"
                      :disabled="trap_disabled || choose != '4'"
                    >
                      <el-radio-button label="+1">+1</el-radio-button>
                      <el-radio-button label="-1">-1</el-radio-button>
                    </el-radio-group>
                  </div>
                </div>
              </el-radio>
            </div>
          </el-radio-group>
          <el-button-group class="operate">
            <div class="current-player">
              <el-text>当前玩家：{{ playerNames[currentPlayer] }}</el-text>
            </div>
            <el-button type="danger" @click="reset">重置</el-button>
            <el-button
              type="primary"
              @click="submit"
              :disabled="submit_disabled"
            >
              提交
            </el-button>
          </el-button-group>
        </div>
        <div
          class="control-section phased-section"
          v-if="globalState === 'settling'"
        >
          <el-button type="primary" @click="comeon">继续</el-button>
        </div>
        <div
          class="control-section finally-section"
          v-if="globalState === 'finished'"
        >
          <!-- 这里的ranking明明是对象，为什么取不出属性id，请回答 -->
          <div>{{ camels[ranking[0]] }}骆驼率先冲线，游戏结束！</div>
          <div>
            冠军：{{ camels[ranking[0]] }}骆驼，垫底：{{
              camels[ranking[4]]
            }}骆驼
          </div>
          <el-button type="primary" @click="again">再来一局</el-button>
        </div>
      </div>
      <div class="right">
        <DiceSection
          class="dice-section"
          :diceStates="diceStates"
          :playerNames="playerNames"
          :camels="camels"
          :colors="colors"
        />
        <ForecastSection
          class="forecast-section"
          :forecast="forecast"
          :playerNames="playerNames"
          :camels="camels"
          :colors="colors"
        />
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
      globalState: "info", //info,initial,wait,running,settling,finished
      playerNum: 3,
      currentPlayer: 0,
      customizeOrNot: false,
      customizePlayerNamesStr: "",
      customizePlayerNames: [],
      defaultPlayerNames: [
        "玩家1",
        "玩家2",
        "玩家3",
        "玩家4",
        "玩家5",
        "玩家6",
        "玩家7",
        "玩家8",
      ],
      playerStates: [],
      raceMileage: 16,
      raceStates: [],
      camels: ["红色", "橙色", "蓝色", "青色", "紫色", "黑色", "白色"],
      colors: [
        "#FF331D",
        "#EDA552",
        "#53A7E7",
        "#53E6B7",
        "#CC6DFC",
        "#000000",
        "#ffffff",
      ],
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
    repeat_prompt() {
      if (this.customizeOrNot) {
        let set = new Set(this.customizePlayerNames);
        return set.size !== this.customizePlayerNames.length;
      } else {
        return false;
      }
    },
    next_able() {
      if (this.customizeOrNot) {
        return (
          this.customizePlayerNames.length < this.playerNum ||
          this.repeat_prompt
        );
      } else {
        return false;
      }
    },
    playerNames() {
      if (this.customizeOrNot) {
        return this.customizePlayerNames.slice(0, this.playerNum);
      } else {
        return this.defaultPlayerNames.slice(0, this.playerNum);
      }
    },
    ranking() {
      let rank = [];
      this.camelStates.slice(0, 5).forEach((camelState, index) => {
        rank.push({
          id: index,
          position: camelState.position,
        });
      });
      rank.sort((a, b) => {
        if (a.position === b.position) {
          let pos = a.position;
          if (pos === -1) return 0;
          let a_index = this.raceStates[pos].camels.indexOf(a.id);
          let b_index = this.raceStates[pos].camels.indexOf(b.id);
          return b_index - a_index;
        } else return b.position - a.position;
      });
      return rank.map((item) => item.id);
    },
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
  watch: {
    customizePlayerNamesStr: {
      handler: function (val) {
        this.customizePlayerNames = val
          .split(/[、,;，；]/)
          .map((item) => item.trim())
          .filter((item) => item != "");
      },
      deep: true,
    },
    trap: {
      handler: function (val) {
        this.raceStates.forEach((raceState) => {
          raceState.traps = [];
        });
        val.forEach((item) => {
          this.raceStates[item.cellId].traps.push({
            type: item.type,
            playerId: item.playerId,
            effect: item.effect,
          });
        });
      },
      deep: true,
    },
    forecast: {
      handler: function (val) {
        this.playerStates.forEach((playerState) => {
          playerState.forecast = [];
        });
        val.forEach((item) => {
          this.playerStates[item.playerId].forecast.push(item.camelId);
        });
      },
      deep: true,
    },
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      this.currentPlayer = 0;
      this.playerStates = [];
      this.raceStates = [];
      this.camelStates = [];
      this.forecast = [];
      this.bet = [];
      this.trap = [];
      this.dices = [];
      this.diceStates = [];
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
      this.singleTrap = {
        type: "",
        playerId: "",
        cellId: "",
      };

      this.playerNames.forEach((playerName, index) => {
        this.playerStates.push({
          id: index,
          name: playerName,
          money: 3,
          forecast: [],
          lotteries: 0,
        });
      });
      this.camels.forEach((camel, index) => {
        if (index === 5 || index === 6) {
          this.camelStates.push({
            id: index,
            name: camel,
            position: this.raceMileage + 1,
            direction: -1,
          });
        } else {
          this.camelStates.push({
            id: index,
            name: camel,
            position: 0,
            direction: 1,
          });
        }
      });
      for (let i = 0; i <= this.raceMileage + 1; i++) {
        this.raceStates.push({
          id: i,
          camels: [],
          traps: [],
        });
      }
      this.camelStates.forEach((camelState) => {
        this.raceStates[camelState.position].camels.push(camelState.id);
      });
      this.camels.slice(0, 5).forEach((camel) => {
        this.bet.push({ camel: camel, players: [] });
      });
      for (let i = 0; i < 6; i++) {
        this.dices.push(i);
      }
    },
    next() {
      this.init();
      // console.log(this.$data);
      this.globalState = "initial";
    },
    skipInitialize() {
      this.camelStates.forEach((camelState) => {
        if (camelState.id === 5 || camelState.id === 6) {
          camelState.position = this.raceMileage + 1;
        } else {
          camelState.position = 0;
        }
      });
      for (let i = 0; i <= this.raceMileage + 1; i++) {
        this.raceStates[i].camels = [];
      }
      this.camelStates.forEach((camelState) => {
        this.raceStates[camelState.position].camels.push(camelState.id);
      });
      this.globalState = "running";
    },
    initialize() {
      this.skipInitialize();
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
      this.singleTrap = {
        type: "",
        playerId: "",
        cellId: "",
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
        // 陷阱结算
        this.trapSettlement();
      } else if (this.globalState === "finished") {
        console.log("游戏结束");
        // 奖券结算
        this.lotterySettlement();
        // 轮注结算
        this.betSettlement();
        // 陷阱结算
        this.trapSettlement();
        // 结果结算
        this.forecastSettlement();
      }
    },
    comeon() {
      // 恢复骰子，等待下一轮
      this.dices = [];
      this.diceStates = [];
      for (let i = 0; i < 6; i++) {
        this.dices.push(i);
      }
      // 恢复奖券，等待下一轮
      this.playerStates.forEach((playerState) => {
        playerState.lotteries = 0;
      });
      // 恢复下注，等待下一轮
      this.bet.forEach((item) => {
        item.players = [];
      });
      // 恢复陷阱，等待下一轮
      this.trap = [];

      this.globalState = "running";
    },
    again() {
      this.init();
      // console.log(this.$data);
      this.globalState = "initial";
    },
    lotterySettlement() {
      this.playerStates.forEach((playerState) => {
        playerState.money += playerState.lotteries;
      });
    },
    betSettlement() {
      let [first, second] = this.ranking.slice(0, 2);
      this.bet.forEach((item, index) => {
        if (index == first) {
          item.players.forEach((playerId, rank) => {
            if (rank == 0) this.playerStates[playerId].money += 5;
            else if (rank == 1) this.playerStates[playerId].money += 3;
            else if (rank == 2) this.playerStates[playerId].money += 2;
            else if (rank == 3) this.playerStates[playerId].money += 2;
          });
        } else if (index == second) {
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
      let [first, last] = [
        this.ranking[0],
        this.ranking[this.ranking.length - 1],
      ];
      let win = [],
        lose = [],
        error = [];
      this.forecast.forEach((item) => {
        if (item.type === "win" && item.camelId === first)
          win.push(item.playerId);
        else if (item.type === "lose" && item.camelId === last)
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
    trapSettlement() {
      this.trap.forEach((item) => {
        if (item.effect) this.playerStates[item.playerId].money += 1;
      });
    },
    camelRace(id, step) {
      let index =
        this.raceStates[this.camelStates[id].position].camels.indexOf(id);
      if (id === 5 || id === 6) {
        if (this.camelStates[id].position === this.raceMileage + 1) {
          this.raceStates[this.camelStates[id].position].camels.splice(
            index,
            1
          );
          this.camelStates[id].position +=
            step * this.camelStates[id].direction;
          this.raceStates[this.camelStates[id].position].camels.push(id);
          this.trapped(id);
        } else {
          let arr =
            this.raceStates[this.camelStates[id].position].camels.splice(index);
          for (let i = 0; i < arr.length; i++) {
            this.camelStates[arr[i]].position +=
              step * this.camelStates[id].direction;
          }
          if (this.camelStates[id].position <= 0) {
            for (let i = 0; i < arr.length; i++) {
              this.camelStates[arr[i]].position = 0;
            }
            // return;
          }
          this.raceStates[this.camelStates[id].position].camels.push(...arr);
          this.trapped(arr[0]);
        }
      } else {
        if (this.camelStates[id].position === 0) {
          this.raceStates[this.camelStates[id].position].camels.splice(
            index,
            1
          );
          this.camelStates[id].position +=
            step * this.camelStates[id].direction;
          this.raceStates[this.camelStates[id].position].camels.push(id);
          this.trapped(id);
        } else {
          let arr =
            this.raceStates[this.camelStates[id].position].camels.splice(index);
          for (let i = 0; i < arr.length; i++) {
            this.camelStates[arr[i]].position +=
              step * this.camelStates[id].direction;
          }
          if (this.camelStates[id].position >= this.raceMileage + 1) {
            for (let i = 0; i < arr.length; i++) {
              this.camelStates[arr[i]].position = this.raceMileage + 1;
            }
            this.globalState = "finished";
            // return;
          }
          this.raceStates[this.camelStates[id].position].camels.push(...arr);
          this.trapped(arr[0]);
        }
      }
    },
    trapped(id) {
      let position = this.camelStates[id].position;
      let move = 0;
      let arr = this.trap.filter((item) => {
        if (item.cellId === position && !item.effect) {
          move += item.type;
          item.effect = true;
          return true;
        }
        return false;
      });
      if (arr.length === 0) return false;
      this.camelRace(id, move);
      return true;
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
        color: this.camels[id],
        step: step,
      });
      this.dices.splice(index, 1);
      this.camelRace(id, step);
      if (this.dices.length === 1 && this.globalState === "running")
        this.globalState = "settling";
    },
    makeForecast() {
      if (
        this.playerStates[this.currentPlayer].forecast.includes(
          Number(this.singleForecast.camelId)
        )
      )
        return;
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
        effect: false,
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
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
}

.info-section {
  height: 100%;
  aspect-ratio: 1.75 / 1;
  max-width: 100vw;
  max-height: 56.25vw;
  position: fixed;
  z-index: 1;
  background-color: #eedfbb;
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
  align-items: center;
}

.info-show {
  height: 100%;
  width: 60%;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: center;
}

.info-show .game-title {
  height: 30%;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  font-size: 8vh;
}

.info-show .info-input {
  height: 60%;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
  align-items: center;
}

.info-show .player-num {
  height: 15%;
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
}

.info-show .player-num .el-text {
  font-size: 3.8vh;
}

.info-show .player-num .el-select {
  width: 8vw;
}

.info-show .player-name {
  height: 85%;
  width: 60%;
  padding: 0 20%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: flex-start;
}

.info-show .player-name > * .el-text {
  font-size: 3vh;
}

.info-show .player-name > *:nth-child(1) {
  margin: 3% 0;
}

.info-show .player-name > *:nth-child(1) .el-text {
  font-size: 3.8vh;
}

.info-show .player-name .el-input {
  width: 30vw;
}

.info-show .player-name .el-text-box .el-text {
  font-size: 2.25vh;
}

.info-show .next-box {
  height: 10%;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.info-show .next-box .el-button {
  width: 20%;
  height: 65%;
  font-size: 3vh;
}

.custom-radio {
  border-color: transparent !important;
}

.rules {
  height: 100%;
  width: 40%;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: center;
  border: 1px solid black;
}

.rules-title {
  height: 10%;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  font-size: 4.5vh;
}

.top {
  height: 30%;
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
  align-items: center;
}

.bottom {
  height: 70%;
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
  align-items: center;
}

.left {
  height: 100%;
  width: 15%;
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
  align-items: center;
}

.middle {
  height: 100%;
  width: 70%;
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
  align-items: center;
}

.right {
  height: 100%;
  width: 15%;
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
  align-items: center;
}

.bet-section {
  height: 25.7%;
  width: 98.4%;
  margin: 1% 0.8% 0.3% 0.8%;
}

.control-section {
  height: 73%;
  width: 100%;
}

.initial-section {
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
  align-items: center;
}

.choose-section {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.choose-section .choose {
  height: 85%;
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  flex-wrap: nowrap;
}

.choose-section .choose-item {
  height: 97%;
  width: 24%;
  margin: 1.5% 0.5%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  background-color: #b3b3b3;
  border-radius: 1.2vh;
}

.choose-item-info {
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: flex-start;
  align-items: center;
  margin: 5% 0;
}

.choose-item-info-title {
  font-size: 2vh;
  margin-right: 1vh;
}

.choose-section .choose-item:nth-child(1) {
  width: 15%;
}

.choose-section .choose-item:nth-child(2) {
  width: 30%;
}

.choose-section .choose-item:nth-child(3) {
  width: 25%;
}

.choose-section .choose-item:nth-child(4) {
  width: 30%;
}

.choose-section .choose-item .choose-title {
  height: 15%;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.choose-section .choose-item .choose-title .el-text {
  font-size: 3.2vh;
}

.choose-section .choose-item .el-radio {
  height: 85%;
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  border-color: transparent;
  border-radius: 1.2vh;
  border: 0.5vh solid #b3b3b3;
  background-color: #cccccc;
}

.choose-section .choose-item .el-select {
  width: 12vh;
  font-size: 2vh !important;
}

.choose-section .operate {
  height: 15%;
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
  align-items: center;
}

.choose-section .operate .current-player .el-text {
  font-size: 3.5vh;
}

.choose-section .operate .el-button {
  height: 80%;
  aspect-ratio: 2 / 1;
  border-radius: 1vh !important;
  font-size: 2.5vh;
}

.phased-section {
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
  align-items: center;
}

.finally-section {
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
  

<style>
.choose-section .forecast .el-radio-button__inner,
.choose-section .bet .el-radio-button__inner,
.choose-section .trap .el-radio-button__inner {
  height: 4vh !important;
  width: 3vw !important;
  font-size: 1.7vh;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  border-radius: 10% !important;
}
</style>
<template>
    <div>
        <div style="display: flex;justify-content: justify-start;">
            <div class="extra" v-if="rounds_extra">
                <bracket :rounds="rounds_extra">
                    <template #player-extension-top="{ match }">
                        <div v-if="match.top" class="top-box">{{ match.top }}</div>
                    </template>
                    <template #player="{ player, match }">
                        {{ player.name }}{{ match.single || '' }}
                    </template>
                    <template #player-extension-bottom="{ match }">
                        <div v-if="match.bottom" class="bottom-box">{{ match.bottom }}</div>
                    </template>
                </bracket>
            </div>
            <div>
                <bracket :rounds="rounds">
                    <template #player-extension-top="{ match }">
                        <div v-if="match.top" class="top-box">{{ match.top }}</div>
                    </template>
                    <template #player="{ player, match }">
                        {{ player.name }}{{ match.single || '' }}
                    </template>
                    <template #player-extension-bottom="{ match }">
                        <div v-if="match.bottom" class="bottom-box">{{ match.bottom }}</div>
                    </template>
                </bracket>
            </div>
        </div>
        <div>
            <bracket v-if="!tennis" :rounds="rounds">
                <template #player-extension-top="{ match }">
                    <div v-if="match.top" class="top-box">{{ match.top }}</div>
                </template>
                <template #player="{ player, match }">
                    {{ player.name }}{{ match.single || '' }}
                </template>
                <template #player-extension-bottom="{ match }">
                    <div v-if="match.bottom" class="bottom-box">{{ match.bottom }}</div>
                </template>
            </bracket>

            <bracket v-else :rounds="rounds">
                <template #player-extension-top="{ match }">
                    <div v-if="match.court || match.group" class="top-box">
                        <text v-if="match.group" class="court">{{ match.group }}组</text>
                        <text v-if="match.court" class="court">{{ match.court }}</text>
                        <text v-if="match.startAt" class="start-at">{{ match.startAt }}</text>
                    </div>
                </template>
                <template #player="{ player, match }">
                    <div class="player">
                        <template v-if="player.signId">
                            <!-- 单打 -->
                            <a-space 
                                v-if="match.single && match.single === 1" 
                                :size="10"
                                :class="[getGroupResultClass(match, player)]">
                                <a-avatar size="default" class="avatar">
                                    {{ (player && player.users) ? player.users[0]['nickName'].slice(0, 1) : '' }}
                                </a-avatar>
                                <!-- 小组赛-签位编号 -->
                                <text class="round-robin-order" v-if="match.round === 0 && player.order">{{ player.order }}</text>
                                <!-- 名字 -->
                                <text class="">{{ (player && player.users) ? player.users[0]['nickName'] : '' }}</text>
                                <!-- 种子 -->
                                <text v-if="player && player.seed" class="seed">[{{ player.seed }}]</text>
                            </a-space>

                            <!-- 双打 -->
                            <a-space v-else-if="match.single && match.single === 2" :size="10" class="doubles">
                                <!-- 头像 -->
                                <div>
                                    <a-avatar size="small" class="avatar" style="margin-bottom:8px;">
                                        {{ (player && player.users) ? player.users[0]['nickName'].slice(0, 1) : '' }}
                                    </a-avatar>
                                    <a-avatar size="small" class="avatar" style="margin-top:8px;margin-left:-5px;">
                                        {{ (player && player.users) ? player.users[1]['nickName'].slice(0, 1) : '' }}
                                    </a-avatar>
                                </div>
                                <!-- 小组赛-签位编号 -->
                                <text class="round-robin-order" v-if="match.round === 0 && player.order">{{ player.order }}</text>
                                <!-- 名字 -->
                                <div>
                                    <text class="name">{{ (player && player.users) ? player.users[0]['nickName'] : '' }}</text>
                                    <text class="name">{{ (player && player.users) ? player.users[1]['nickName'] : '' }}</text>
                                </div>
                                <!-- 种子 -->
                                <text v-if="player && player.seed" class="seed">[{{ player.seed }}]</text>
                            </a-space>
                        </template>
                        <!-- 默认空 -->
                        <template v-else>
                            <a-space :size="5">
                                <a-avatar size="default" style="opacity: 0;" />
                                <!-- <text class="" space="true">TBD</text> -->
                            </a-space>
                        </template>
                        <div>
                            <!-- 胜利 ✅图标 -->
                            <CheckOutlined
                                v-if="player.winner || (match.round === 0 && player.rank <= match.riseCount && match.groupCompleted)"
                                style="color:#87d068;font-size:17px;line-height:32px;" />
                            <div class="round-robin-rank" v-if="match.round === 0 && player.rank">{{ player.rank }}</div>
                            <div class="champion" v-if="match.title === '决赛' && player.winner">冠军</div>
                        </div>
                    </div>
                </template>
                <template #player-extension-bottom="{ match }">
                    <!-- 底部 比分栏 -->
                    <div v-if="match.scores && match.scores.length" class="bottom-box">
                        <a-space align="center" size="middle">
                            <text v-for="(set, key, index) in match.scores" :key="index" class="">
                                <text :class="getScoreClass(set, 0)">{{ set[0] }}</text> - <text
                                    :class="getScoreClass(set, 1)">{{ set[1] }}</text>
                            </text>
                        </a-space>
                    </div>
                </template>
            </bracket>
        </div>
    </div>
</template>

<script>
import Bracket from "./Bracket";
import axios from 'axios';

const rounds_x = [
    //Quarter
    {
        games: [
            {
                player1: { id: "1", name: "Competitor 1", winner: true },
                player2: { id: "2", name: "Competitor 2", winner: false },
                // player3: { id: "2", name: "Competitor 2", winner: false },
                // player4: { id: "2", name: "Competitor 2", winner: false },
            },
            {
                player1: { id: "3", name: "Competitor 3", winner: false },
                player2: { id: "4", name: "Competitor 4", winner: true },
            },
            {
                player1: { id: "5", name: "Competitor 5", winner: true },
                player2: { id: "6", name: "Competitor 6", winner: false },
            },
            {
                player1: { id: "7", name: "Competitor 7", winner: false },
                player2: { id: "8", name: "Competitor 8", winner: true },
            },
        ],
    },
    //Semi
    {
        games: [
            {
                player1: { id: "1", name: "Competitor 1", winner: false },
                player2: { id: "4", name: "Competitor 4", winner: true },
            },
            {
                player1: { id: "5", name: "Competitor 5", winner: false },
                player2: { id: "8", name: "Competitor 8", winner: true },
            },
        ],
    },
    //Final
    {
        games: [
            {
                player1: { id: "4", name: "Competitor 4", winner: false },
                player2: { id: "8", name: "Competitor 8", winner: true },
            },
        ],
    },
];

const rounds_y = [
    // Round Robin
    {
        title: "小组循环",
        type: "roundRobin",
        games: [
            {
                single: 'single',
                players: [
                    { id: "1", name: "Competitor 1", winner: true },
                    { id: "2", name: "Competitor 2", winner: false },
                    { id: "9", name: "Competitor 9", winner: false },
                    { id: "10", name: "Competitor 10", winner: true },
                ],
            },
            { players: [] },
            {
                players: [
                    { id: "3", name: "Competitor 3", winner: false },
                    { id: "4", name: "Competitor 4", winner: true },
                    { id: "11", name: "Competitor 11", winner: false },
                    { id: "12", name: "Competitor 12", winner: true },
                ]
            },
            { players: [] },
            { players: [] },
            {
                players: [
                    { id: "5", name: "Competitor 5", winner: true },
                    { id: "6", name: "Competitor 6", winner: false },
                    { id: "13", name: "Competitor 13", winner: true },
                    { id: "14", name: "Competitor 14", winner: false },
                ],
            },
            { players: [] },
            {
                players: [
                    { id: "7", name: "Competitor 7", winner: false },
                    { id: "8", name: "Competitor 8", winner: true },
                    { id: "15", name: "Competitor 15", winner: false },
                    { id: "16", name: "Competitor 16", winner: true },
                ],
            },
        ],
    },
    // Quarter
    {
        title: "1/4决赛",
        games: [
            {
                top: '中央球场',
                players: [
                    { id: "1", name: "Competitor 1", winner: false },
                    { id: "12", name: "Competitor 12", winner: true },
                ],
                scores: [1, 2, 3, 4],
                bottom: '6:3 2:6 7:5'
            },
            {
                players: [
                    { id: "4", name: "Competitor 4", winner: false },
                    { id: "13", name: "Competitor 13", winner: true },
                ],
            },
            {
                players: [
                    { id: "5", name: "Competitor 5", winner: false },
                    { id: "16", name: "Competitor 16", winner: true },
                ],
            },
            {
                players: [
                    { id: "8", name: "Competitor 8", winner: false },
                    { id: "10", name: "Competitor 10", winner: true },
                ],
            },
        ],
    },
    // Semi
    {
        title: "半决赛",
        games: [
            {
                players: [
                    { id: "1", name: "Competitor 1", winner: false },
                    { id: "4", name: "Competitor 4", winner: true },
                ],
            },
            {
                players: [
                    { id: "5", name: "Competitor 5", winner: false },
                    { id: "8", name: "Competitor 8", winner: true },
                ],
            },
        ],
    },
    // Third place play off
    {
        title: "3、4名决赛",
        games: [
            {
                players: [
                    { id: "1", name: "Competitor 1", winner: false },
                    { id: "5", name: "Competitor 5", winner: true },
                ],
            },
        ],
    },
    // Final
    {
        title: "决赛",
        games: [
            {
                players: [
                    { id: "4", name: "Competitor 4", winner: false },
                    { id: "8", name: "Competitor 8", winner: true },
                ],
            },
        ],
    },
];

const rounds = [
    // Quarter
    {
        title: "1/4决赛",
        games: [
            {
                // top: '中央球场',
                players: [
                    { id: "1", name: "Competitor 1", winner: false },
                    { id: "5", name: "Competitor 5", winner: true },
                ],
                // scores: [1,2,3,4],
                // bottom: '6:3 2:6 7:5'
            },
            {
                players: [
                    { id: "2", name: "Competitor 2", winner: false },
                    { id: "6", name: "Competitor 6", winner: true },
                ],
            },
            {
                players: [
                    { id: "3", name: "Competitor 3", winner: false },
                    { id: "7", name: "Competitor 7", winner: true },
                ],
            },
            {
                players: [
                    { id: "4", name: "Competitor 4", winner: false },
                    { id: "8", name: "Competitor 8", winner: true },
                ],
            },
        ],
    },
    // Semi
    {
        title: "半决赛",
        games: [
            {
                players: [
                    { id: "5", name: "Competitor 5", winner: false },
                    { id: "6", name: "Competitor 6", winner: true },
                ],
            },
            {
                players: [
                    { id: "7", name: "Competitor 7", winner: false },
                    { id: "8", name: "Competitor 8", winner: true },
                ],
            },
        ],
    },
    // Third place play off
    {
        title: "3、4名决赛",
        games: [
            {
                players: [
                    { id: "5", name: "Competitor 5", winner: false },
                    { id: "7", name: "Competitor 7", winner: true },
                ],
            },
        ],
    },
    // Final
    {
        title: "决赛",
        games: [
            {
                players: [
                    { id: "6", name: "Competitor 6", winner: false },
                    { id: "8", name: "Competitor 8", winner: true },
                ],
            },
        ],
    },
];

    // Semi
const rounds_extra = [
    // Quarter
    {
        title: "1/4决赛",
        games: [
            {
                // top: '中央球场',
                players: [
                    { id: "1", name: "Competitor 1", winner: false },
                    { id: "5", name: "Competitor 5", winner: true },
                ],
                // scores: [1,2,3,4],
                // bottom: '6:3 2:6 7:5'
            },
            {
                players: [
                    { id: "2", name: "Competitor 2", winner: false },
                    { id: "6", name: "Competitor 6", winner: true },
                ],
            },
            {
                players: [
                    { id: "3", name: "Competitor 3", winner: false },
                    { id: "7", name: "Competitor 7", winner: true },
                ],
            },
            {
                players: [
                    { id: "4", name: "Competitor 4", winner: false },
                    { id: "8", name: "Competitor 8", winner: true },
                ],
            },
        ],
    },
    {
        title: "附加：争5-8名",
        games: [
            {
                players: [
                    { id: "5", name: "Competitor 1", winner: false },
                    { id: "6", name: "Competitor 2", winner: true },
                ],
            },
            {
                players: [
                    { id: "7", name: "Competitor 3", winner: false },
                    { id: "8", name: "Competitor 4", winner: true },
                ],
            },
        ],
    },
    // Third place play off
    {
        title: "附加：争7、8名",
        games: [
            {
                players: [
                    { id: "5", name: "Competitor 1", winner: false },
                    { id: "7", name: "Competitor 3", winner: true },
                ],
            },
        ],
    },
    // Final
    {
        title: "附加：争5、6名",
        games: [
            {
                players: [
                    { id: "6", name: "Competitor 2", winner: false },
                    { id: "8", name: "Competitor 4", winner: true },
                ],
            },
        ],
    },
];

console.log(rounds_x, rounds_y);

export default {
    name: "app",
    components: {
        Bracket,
    },
    data() {
        return {
            tennis: true,
            rounds: rounds,
            rounds_extra: rounds_extra,
        };
    },
    mounted() {
        this.tennis && this.getDrawData();
    },
    methods: {
        getScoreClass(set, current) {
            let win = set[0] > set[1];
            win = current === 0 ? win : !win;
            return win ? 'score-win' : 'score-loss';
        },
        // 小组赛结束后的 淘汰样式
        getGroupResultClass(match, player) {
            return match.round === 0 && player.stage === 0 && player.rank > match.riseCount && match.groupCompleted ? 'defeated' : '';
        },
        async getDrawData() {
            let sectionId = document.location.search.split('=')[1];
            sectionId = sectionId || '341984566057109154';
            // const sectionId = '222371';
            // sectionId = '350432587778360184';
            if (!sectionId) { return; }
            const token = `Bearer eyJpdiI6IitEVjluZ3lGdzBhU3RFQWV1MWdCR0E9PSIsInZhbHVlIjoiRmFyc01MK3lmb3l2di9nZGU2UisrZ2crdTBNdVN4Q1pWT0ZUanc5UUNxZVNMdWhSNGlYd0NOVytsMVV3Q2kwRSIsIm1hYyI6IjExMzY3YzhhNGNhM2YwNDhlMjdiMjJjNDljMGQ5MGMxNDMxMDY5ZTY3OTIxMjU1NWFlMzU1MDBhY2Q3NjZlNjYiLCJ0YWciOiIifQ==`;
            const ret = await axios.get("/api/tennis/section/draw", {
                headers: {
                    'Authorization': token,
                },
                params: { sectionId },
            });

            this.rounds = ret.data.data.rounds;
        }
    }
};
</script>

<style>
/* 签表样式 */
body {
  background-color: #f7f7f7;
}

.top-box {
  position: relative;
  padding: 4px 10px;
  font-size: 13px;
  background-color: #eeededea;
  display: flex;
  justify-content: space-between;
}

.bottom-box {
  font-size: 13px;
  padding: 8px;
  position: relative;
  text-align: center;
}

.bottom-box:after {
    content: '';
    top: 0; left: 10%; right: 0;
    width: 80%;
    height: 1px;
    position: absolute;
    z-index: 1;
    background: #eeededea;
}




.top-box:after {
  content: "";
  bottom: 0;
  left: 0;
  right: 0;
  width: 100%;
  height: 1px;
  position: absolute;
  z-index: 1;
  background: #eeededea;
}

.bottom-box:after {
  content: "";
  top: 0;
  left: 10%;
  right: 0;
  width: 80%;
  height: 1px;
  position: absolute;
  z-index: 1;
  background: #eeededea;
}

.player {
  min-width: 250px;
  display: flex;
  justify-content: space-between;
  align-content: center;
}

.defeated .ant-avatar {
  opacity: 0.5;
}

.score-loss {
  color: rgb(167, 165, 165);
}

.start-at {
  color: grey;
  font-size: 12px;
}

.avatar {
  background-color: #87d068;
}

.doubles .name {
  display: block;
  font-size: 13px;
  height: 16px;
  line-height: 16px;
}

.seed {
  border-left: 1px solid #e5e4e4;
  padding-left: 10px;
}

.round-robin-rank {
  display: inline-block;
  padding: 0 10px;
  color: grey;
}

.champion {
  display: inline-block;
  padding: 0 10px;
  color: #e2bf04;
}

.round-robin-order {
  color: grey;
}

/* 签表覆盖样式 */
.vtb-wrapper {
  width: fit-content;
}
</style>
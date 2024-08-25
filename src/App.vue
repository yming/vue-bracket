<template>
    <div style="display: flex;justify-content: justify-start;">
        <div class="extra">
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
</template>

<script>
import Bracket from "./Bracket";

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
                scores: [1,2,3,4],
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
            rounds: rounds,
            rounds_extra: rounds_extra,
        };
    },
};
</script>

<style>
    body {
        background-color: #f7f7f7;
    }

    .top-box {
        position: relative;
        padding: 4px 10px;
        font-size: 13px;
        background-color: #eeededea;
    }
    
    .bottom-box {
        font-size: 13px;
        padding: 8px;
        position: relative;
        text-align: center;
    }

    .top-box:after {
        content: '';
        bottom: 0; left: 0; right: 0;
        width: 100%;
        height: 1px;
        position: absolute;
        z-index: 1;
        background: #eeededea;
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




</style>
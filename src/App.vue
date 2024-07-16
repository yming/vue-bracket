<template>
    <bracket :rounds="rounds">
        <template #player-extension-top="{ match }">
            <div>{{ match.top }}</div>
        </template>
        <template #player="{ player }">
            {{ player.name }}
        </template>
        <template #player-extension-bottom="{ match }">
            <div>{{ match.bottom }}</div>
        </template>
    </bracket>
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

const roundRobins = [
    // Round Robin
    {
        title: "小组循环",
        type: "roundRobin",
        games: [
            [
                { id: "1", name: "Competitor 1", winner: true },
                { id: "2", name: "Competitor 2", winner: false },
                { id: "9", name: "Competitor 9", winner: false },
                { id: "10", name: "Competitor 10", winner: true },
            ],
            [],
            [
                { id: "3", name: "Competitor 3", winner: false },
                { id: "4", name: "Competitor 4", winner: true },
                { id: "11", name: "Competitor 11", winner: false },
                { id: "12", name: "Competitor 12", winner: true },
            ],
            [],
            [],
            [
                { id: "5", name: "Competitor 5", winner: true },
                { id: "6", name: "Competitor 6", winner: false },
                { id: "13", name: "Competitor 13", winner: true },
                { id: "14", name: "Competitor 14", winner: false },
            ],
            [],
            [
                { id: "7", name: "Competitor 7", winner: false },
                { id: "8", name: "Competitor 8", winner: true },
                { id: "15", name: "Competitor 15", winner: false },
                { id: "16", name: "Competitor 16", winner: true },
            ],
        ],
    },
];

const rounds = [
    // Round Robin
    {
        title: "小组循环",
        type: "roundRobin",
        games: [
            {
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

console.log(rounds_x, roundRobins);

export default {
    name: "app",
    components: {
        Bracket,
    },
    data() {
        return {
            rounds: rounds,
        };
    },
};
</script>

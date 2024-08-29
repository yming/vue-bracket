<template>
    <div class="vtb-item" :class="[isTopLeft(bracketNode)]" v-if="playersArePresent">
        <div :class="getBracketNodeClass(bracketNode)">
            <!-- Round Title -->
            <!-- <div class="round-title" v-if="!bracketNode.gameIndex && getBracketNodeClass(bracketNode) === 'vtb-item-parent'">{{ bracketNode.title }}</div> -->
            <div class="round-title" v-if="!bracketNode.gameIndex">{{ bracketNode.title }}</div>
            <game-players
                :bracket-node="bracketNode"
                :highlighted-player-id="highlightedPlayerId"
                @onSelectedPlayer="highlightPlayer"
                @onDeselectedPlayer="unhighlightPlayer"
            >
                <template #player-extension-top="{ match }">
                    <slot name="player-extension-top" :match="match" />
                </template>
                <template #player="{ player, match }">
                    <slot name="player" :player="player" :match="match" />
                </template>
                <template #player-extension-bottom="{ match }">
                    <slot name="player-extension-bottom" :match="match" />
                </template>
            </game-players>
        </div>

        <div v-if="bracketNode.games[0] || bracketNode.games[1]" class="vtb-item-children" :class="[getRoundRobinClass(bracketNode.games[0])]">
            <div class="vtb-item-child" v-if="bracketNode.games[0]" :class="[isPlaceHolder(bracketNode.games[0]), isNoChildren(bracketNode.games[0])]">
                <!-- Round Title -->
                <!-- <div class="round-title" v-if="!bracketNode.games[0].gameIndex && (bracketNode.games[0].round === 0)">{{ bracketNode.games[0].title }}</div> -->
                <bracket-node
                    :bracket-node="bracketNode.games[0]"
                    :highlighted-player-id="highlightedPlayerId"
                    @onSelectedPlayer="highlightPlayer"
                    @onDeselectedPlayer="unhighlightPlayer"
                >
                    <template #player-extension-top="{ match }">
                        <slot name="player-extension-top" :match="match" />
                    </template>
                    <template #player="{ player, match }">
                        <slot name="player" :player="player" :match="match" />
                    </template>
                    <template #player-extension-bottom="{ match }">
                        <slot name="player-extension-bottom" :match="match" />
                    </template>
                </bracket-node>
            </div>
            <div class="vtb-item-child" v-if="bracketNode.games[1]" :class="[isPlaceHolder(bracketNode.games[1]), isNoChildren(bracketNode.games[1])]">
                <bracket-node
                    :bracket-node="bracketNode.games[1]"
                    :highlighted-player-id="highlightedPlayerId"
                    @onSelectedPlayer="highlightPlayer"
                    @onDeselectedPlayer="unhighlightPlayer"
                >
                    <template #player-extension-top="{ match }">
                        <slot name="player-extension-top" :match="match" />
                    </template>
                    <template #player="{ player, match }">
                        <slot name="player" :player="player" :match="match" />
                    </template>
                    <template #player-extension-bottom="{ match }">
                        <slot name="player-extension-bottom" :match="match" />
                    </template>
                </bracket-node>
            </div>
        </div>
    </div>
</template>

<script>
    import GamePlayers from "./GamePlayers";

    export default {
        name: "bracket-node",
        components: { GamePlayers },
        props: ["bracketNode", "highlightedPlayerId"],
        computed: {
            playersArePresent() {
                return this.bracketNode.players[1];
                // return this.bracketNode.player1 && this.bracketNode.player1;
            },
            // emptyItemPresent() {
            //     return this.bracketNode.games.type === 'roundRobin'
            // }
        },
        methods: {
            getBracketNodeClass(bracketNode) {
                if (bracketNode.games[0] || bracketNode.games[1]) {
                    return "vtb-item-parent";
                }

                if (bracketNode.hasParent) {
                    return "vtb-item-child";
                }

                return "";
            },
            getPlayerClass(player) {
                if (player.winner === null || player.winner === undefined) {
                    return "";
                }

                let clazz = player.winner ? "winner" : "defeated";

                if (this.highlightedPlayerId === player.id) {
                    clazz += " highlight";
                }

                return clazz;
            },
            getRoundRobinClass(bracketNode) {
                return bracketNode && bracketNode.type === 'roundRobin' ? 'round-robin' : '';
            },

            isTopLeft(bracketNode) {
                if(bracketNode.games.length === 0 && bracketNode.gameIndex === 0) {
                    return 'top-left';
                } else {
                    return '';
                }
            },
            getRoundTitle() {

            },
            isPlaceHolder(bracketNode) {
                return bracketNode.placeholder ? 'placeholder' : '';
            },
            isNoChildren(bracketNode) {
                return bracketNode.games.length === 0 ? 'vtb-item-leaf' : '';
            },
            highlightPlayer(playerId) {
                this.$emit("onSelectedPlayer", playerId);
            },
            unhighlightPlayer() {
                this.$emit("onDeselectedPlayer");
            },
        },
    };
</script>

<style>
    .vtb-item {
        display: flex;
        flex-direction: row-reverse;
    }

    .vtb-item p {
        padding: 20px;
        margin: 0;
        background-color: #999999;
    }

    .vtb-item-parent {
        position: relative;
        margin-left: 50px;
        display: flex;
        align-items: center;
    }

    .vtb-item-players {
        flex-direction: column;
        background-color: white;
        margin: 0;
        color: black;
        border-radius: 5px;
        border: 1px solid #e5e4e4;
    }

    .vtb-item-players .vtb-player {
        padding: 10px;
        white-space: nowrap;
        position: relative;
    }

    .vtb-item-players .vtb-player:after {
        content: '';
        bottom: 0; left: 10%; right: 0;
        width: 80%;
        height: 1px;
        position: absolute;
        z-index: 1;
        background: #eeededea;
    }

    .vtb-item-players .highlight:after {
        display: none;
    }

    .vtb-item-players .vtb-player:last-child:after {
        display: none;
    }

    .vtb-item-players .defeated {
        /* background-color: firebrick; */
        color: rgb(147, 146, 146);
    }

    .vtb-item-players .winner.highlight {
        background-color: darkseagreen;
        color: white;
    }

    .vtb-item-players .defeated.highlight {
        background-color: indianred;
        color: white;
    }

    .vtb-item-parent:after {
        position: absolute;
        content: "";
        width: 25px;
        height: 2px;
        left: 0;
        top: 50%;
        background-color: rgb(170, 169, 169);
        transform: translateX(-100%);
    }

    .vtb-item-children {
        display: flex;
        flex-direction: column;
        justify-content: center;

        position: relative;

        /* 纵向通长虚线 */
        padding-right: 24px;
        margin-right: -25px;
        border-right: 1px dashed #33333330;
    }

    .vtb-item-child {
        display: flex;
        align-items: flex-start;
        justify-content: flex-end;
        /* margin-top: 2px;
        margin-bottom: 2px; */
        position: relative;
    }

    .vtb-item-child:before {
        content: "";
        position: absolute;
        background-color: rgb(170, 169, 169);
        right: 0;
        top: 50%;
        transform: translateX(100%);
        width: 25px;
        height: 2px;
    }

    .vtb-item-child:after {
        content: "";
        position: absolute;
        background-color: rgb(170, 169, 169);
        right: -25px;
        height: calc(50% + 22px);
        width: 2px;
        top: 50%;
    }

    .vtb-item-child:last-child:after {
        transform: translateY(-100%);
    }

    .vtb-item-child:only-child:after {
        display: none;
    }



    /* For Round Robins  */
    .vtb-item-child:empty:before {
        display: none;
    }

    .vtb-item.top-left .vtb-item-child:before {
        display: none;
    }

    .round-robin .vtb-item-child:after {
        height: 200%;
    }

    /* 小组循环填充位 占位用 */
    .round-robin .placeholder {
        height: 20px;
    }

    .round-robin .placeholder::before {
        display: none;
    }

    .round-robin .placeholder .vtb-item {
        opacity: 0;
    }

    .round-robin .placeholder .vtb-item-child:before  {
        display: none;
    }

    .round-title {
        position: absolute;
        top: 0;
        text-align: center;
        font-size: 13px;
        width: 100%;
        color: rgb(113, 113, 113);
    }

    .top-left {
        .vtb-item-players {
            margin-top: 30px;
        }
    }

    /* 签表覆盖样式 */
    .vtb-item-leaf {
        margin-bottom: 4px;
    }

    .round-robin .vtb-item-leaf {
        margin-bottom: 0;
    }

    .round-robin .vtb-item-child {
        padding-top: 1px;
        padding-bottom: 1px;
    }

    .round-robin .vtb-item-child:after {
        height: 100% !important;
        top: 0;
        background-color: rgb(170, 169, 169);;
    }

    .top-left .vtb-item-child:after {
        top: 20px;
    }

    .round-robin .vtb-item-child:last-child:after {
        transform: translateY(0%);
    }

    /* 附加赛 */
    .extra {
        .vtb-item {
            flex-direction: row !important;
        }

        .vtb-item-parent {
            margin-left: 0;
            margin-right: 50px;
        }

        .vtb-item-parent:after {
            width: 0 !important;
        }

        .vtb-item-parent:before {
            position: absolute;
            content: "";
            width: 25px;
            height: 2px;
            right: 0;
            top: 50%;
            background-color: rgb(170, 169, 169);
            -webkit-transform: translateX(100%);
            transform: translateX(100%);
        }

        .vtb-item-children {
            display: -webkit-box;
            display: -ms-flexbox;
            display: flex;
            -webkit-box-orient: vertical;
            -webkit-box-direction: normal;
            -ms-flex-direction: column;
            flex-direction: column;
            -webkit-box-pack: center;
            -ms-flex-pack: center;
            justify-content: center;
            position: relative;
            padding-left: 24px;
            margin-left: -25px;
            border-left: 1px dashed #33333330;
            border-right: none;
        }

        .vtb-item-child:before {
            content: "";
            position: absolute;
            background-color: rgb(170, 169, 169);
            right: auto;
            left: 0;
            top: 50%;
            -webkit-transform: translateX(-100%);
            transform: translateX(-100%);
            width: 25px;
            height: 2px;
        }

        .vtb-item-child:last-child:after {
            -webkit-transform: translateY(-100%);
            transform: translateY(-100%);
        }


        .vtb-item-child:after {
            content: "";
            position: absolute;
            background-color: rgb(170, 169, 169);
            left: -25px;
            height: calc(50% + 22px);
            width: 2px;
            top: 50%;
        }

        .vtb-item-leaf {
            .round-title {
                display: none;
            }
            .vtb-item {
                width: 0;
                overflow: hidden;
            }
        }
    }
</style>

<template>
    <div class="vtb-wrapper" v-if="recursiveBracket">
        <bracket-node
            :bracket-node="recursiveBracket"
            @onSelectedPlayer="highlightPlayer"
            @onDeselectedPlayer="unhighlightPlayer"
            :highlighted-player-id="highlightedPlayerId"
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
</template>

<script>
    import BracketNode from "./components/BracketNode";
    import recursiveBracket from "./components/recursiveBracket";

    export default {
        name: "bracket",
        components: {
            "bracket-node": BracketNode,
        },
        props: ["rounds", "flatTree"],
        data() {
            return {
                highlightedPlayerId: null,
            };
        },
        computed: {
            recursiveBracket() {
                if (this.flatTree) {
                    return recursiveBracket.transformFlatTree(this.flatTree);
                }

                return recursiveBracket.transform(this.rounds);
            },
        },
        methods: {
            highlightPlayer() {
                // this.highlightedPlayerId = id;
            },
            unhighlightPlayer() {
                // this.highlightedPlayerId = null;
            },
        },
    };
</script>

<style>
    .vtb-wrapper {
        display: flex;
    }
</style>

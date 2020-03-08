<template>
    <div
    class="ghostItem"
    @mouseover="getChosenPosition(position)"
    :style="{
    gridRow: position.row + '/' + (position.row + 1),
    gridColumn: position.column + '/' + (position.column + 1)
    }"
    ></div>
</template>

<script>
    export default {
        props: {
            pAtt: Object,
            getChosenPosition: Function
        },
        data(){
            return {
                position: null
            }
        },
        created() {
            this.position = this.pAtt
        }
    }
</script>

<style scoped>
    .ghostItem{

        -webkit-user-select: none;
        -khtml-user-select: none;
        -moz-user-select: none;
        -o-user-select: none;
        user-select: none;

        -webkit-user-drag: none;
        -khtml-user-drag: none;
        -moz-user-drag: none;
        -o-user-drag: none;
        user-drag: none;
    }
</style>
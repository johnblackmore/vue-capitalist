<template>
    <div class="row">
        <div class="col-xs-8 text-right">
            <p class="lead">Bank Balance: ${{ balance.formatMoney(2) }}</p>
        </div>
        <div class="col-xs-4">
            <button class="btn btn-success btn-block navbar-btn" v-if="!gameStarted" @click="startGame(true)">Start
                Game!
            </button>
            <button class="btn btn-danger btn-block navbar-btn" v-if="gameStarted" @click="startGame(false)">Stop!
            </button>
        </div>
    </div>
</template>

<script>
    import {eventBus} from '../main'

    export default {
        name: 'appHeader',
        props: ['balance', 'gameStarted'],
        methods: {
            startGame (started) {
                eventBus.$emit('startGame', started)
            }
        }
    }
</script>

<style lang="scss">

</style>
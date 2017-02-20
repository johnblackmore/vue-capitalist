<template>
    <div id="app">
        <div class="well">
            <div class="container">
                <div class="col-xs-12 col-md-6">
                    <span class="lead">Vue JS Capitalist</span>
                </div>
                <div class="col-xs-12 col-md-6">
                    <app-header
                            :balance="balance"
                            :gameStarted="gameStarted"
                            :startGame="startGame"></app-header>
                </div>
            </div>
        </div>
        <div class="container">
            <app-business
                    name="Lemonade Stand"
                    initialCount="1"
                    initialCost="10"
                    profit="1"
                    time="8"
                    manager="50"
                    :tick="gameTime"
                    :balance="balance"
                    :gameStarted="gameStarted"></app-business>
            <app-business
                    name="Paper Round"
                    initialCount="0"
                    initialCost="100"
                    profit="10"
                    time="16"
                    manager="500"
                    :tick="gameTime"
                    :balance="balance"
                    :gameStarted="gameStarted"></app-business>
            <app-business
                    name="Coffee Shop"
                    initialCount="0"
                    initialCost="1000"
                    profit="100"
                    time="32"
                    manager="5000"
                    :tick="gameTime"
                    :balance="balance"
                    :gameStarted="gameStarted"></app-business>
            <app-business
                    name="Property Landlord"
                    initialCount="0"
                    initialCost="10000"
                    profit="1000"
                    time="64"
                    manager="50000"
                    :tick="gameTime"
                    :balance="balance"
                    :gameStarted="gameStarted"></app-business>
            <div class="row">
                <div class="col-xs-12 col-md-2 col-md-offset-10">
                    Game Time: {{ gameTime }}
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import { eventBus } from './main'
    import appHeader from './components/Header.vue'
    import appBusiness from './components/Business.vue'

    export default {
        name: 'app',
        data () {
            return {
                gameTime: 0,
                gameStarted: false,
                balance: 0
            }
        },
        components: {
            appHeader,
            appBusiness
        },
        methods: {
            tick () {
                this.gameTime++
            },
            startGame(started) {
                if (started) {
                    // Start the game
                    this.gameTime = 0
                    this.balance = 0
                    this.gameStarted = true
                    this.timer = setInterval(function () {
                        eventBus.$emit('tick')
                    }, 250)
                    return
                }

                // Stop the game
                this.gameStarted = false
                clearInterval(this.timer)
            }
        },
        created () {
            eventBus.$on('balanceIncrease', (amount) => {
                this.balance += amount
            })
            eventBus.$on('balanceDecrease', (amount) => {
                this.balance -= amount
            })
            eventBus.$on('startGame', (started) => {
                this.startGame(started)
            })
            eventBus.$on('tick', () => {
                this.tick()
            })
        }
    }
</script>

<style lang="scss">
    body {
        padding-top: 70px;
    }
</style>

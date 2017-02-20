<template>
    <div class="row">
        <!--<div class="col-xs-12 col-md-4"><p>-->
        <!--Name: {{ name }}<br>-->
        <!--Count: {{ count }}<br>-->
        <!--Cost: {{ cost }}<br>-->
        <!--Profit: {{ profit }}<br>-->
        <!--Time: {{ time }}<br>-->
        <!--Tick: {{ tick }}<br>-->
        <!--Last Redeemed Tick: {{ lastRedeemedTick }}<br>-->
        <!--Progress: {{ progress }}%-->
        <!--</p></div>-->
        <div class="col-xs-12">
            <h3>{{ name }}</h3>
            <div class="row">
                <div class="col-xs-12 col-md-8">
                    <div class="progress">
                        <div class="progress-bar" role="progressbar" :style="{width: progress + '%'}"
                             :class="{'no-anim': tick == lastRedeemedTick + 1}">
                            {{ progress }}%
                        </div>
                    </div>
                </div>
                <div class="col-xs-12 col-md-4" v-show="gameStarted">
                    <div class="row">
                        <button class="btn btn-success col-xs-6" :disabled="progress != 100" @click="redeem">Redeem
                        </button>
                        <button class="btn btn-info col-xs-6" :disabled="balance < cost" @click="buy">Buy</button>
                    </div>
                </div>
            </div>
            <div class="row">
                <div class="col-sm-2">Count: {{ count }}</div>
                <div class="col-sm-2">Cost: ${{ parseFloat(cost).formatMoney(0) }}</div>
                <div class="col-sm-2">Profit: ${{ parseFloat(profit * count).formatMoney(0) }}</div>
                <div class="col-sm-2">Manager: ${{ parseFloat(manager).formatMoney(0) }}
                    <button class="btn btn-success btn-xs" :disabled="balance < manager" @click="manage"
                            v-if="!managed && gameStarted">Hire
                    </button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import {eventBus} from '../main'

    export default {
        name: 'appBusiness',
        data () {
            return {
                'count': 0,
                'cost': 0,
                'lastRedeemedTick': 0,
                'managed': false
            }
        },
        computed: {
            progress () {
                if (this.count == 0) {
                    return 0
                }
                return Math.min(Math.floor((this.tick - this.lastRedeemedTick) / this.time * 100), 100)
            }
        },
        'props': ['name', 'initialCount', 'initialCost', 'profit', 'time', 'tick', 'balance', 'gameStarted', 'manager'],
        created () {
            eventBus.$on('startGame', (started) => {
                if (started) {
                    this.count = this.initialCount
                    this.cost = this.initialCost
                    this.lastRedeemedTick = 0
                    this.managed = false
                }
            })
            eventBus.$on('tick', () => {
                if (this.progress == 100 && this.managed) {
                    this.redeem()
                }
            })
        },
        methods: {
            redeem () {
                this.lastRedeemedTick = this.tick
                eventBus.$emit('balanceIncrease', this.profit * this.count)
            },
            buy () {
                if (this.count == 0) {
                    this.lastRedeemedTick = this.tick
                }
                // decrease balance by cost, increment count
                eventBus.$emit('balanceDecrease', this.cost)
                this.count++
                this.cost = Math.floor(this.cost * 1.1)
            },
            manage () {
                eventBus.$emit('balanceDecrease', this.manager)
                this.managed = true
            }
        }
    }
</script>

<style lang="scss" scoped>
    .progress-bar {
        min-width: 2em;
        transition: 0.2s linear;
    }

    .no-anim {
        transition: none;
    }
</style>
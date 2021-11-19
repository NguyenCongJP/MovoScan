<template>
    <div>
        <section class="bg-dark" style="background-image: url(/background.png); background-repeat: repeat;">
            <div class="container space-top-2 space-bottom-3">
                <div class="row justify-content-between align-items-center mb-4">
                    <div class="col-md-12 col-lg-7">
                        <div class="pr-lg-4 pr-xll-5">
                            <h1 class="h3 mb-3" style="color: white;">Movo Chain Testnet Explorer</h1>
                            <search-details />
                        </div>
                    </div>
                    <div class="col-12 col-lg-5">
                        <div class="d-none d-lg-block text-center pl-lg-4 pl-xll-5"></div>
                    </div>
                </div>
            </div>
        </section>
        <section class="bg-dark" style="background-image: url(/background-5-percent.png); background-repeat: repeat;">
            <div class="container space-bottom-1 mt-n5">
                <div class="row mb-5">
                    <div class="col-lg-6 mb-4 mb-lg-0">
                        <recent-blocks :max-items="maxItems" :topic-block="topicBlock" page-type="home" @errorBlocksList="setError" />
                    </div>
                    <div class="col-lg-6 mb-4 mb-lg-0">
                        <home-txs
                            :max-items="maxItems"
                            :topic-transaction="topicTransaction"
                            :new-block="newBlockNumber"
                            page-type="home"
                            @errorTxs="setError"
                        />
                    </div>
                </div>
            </div>
        </section>
    </div>
</template>

<script lang="ts">
import SearchDetails from '@app/modules/search/handlers/SearchDetails.vue'
import AppBreadCrumbs from '@app/core/components/ui/AppBreadCrumbs.vue'
import AppMessage from '@app/core/components/ui/AppMessage.vue'
import BlockStats from '@app/modules/blocks/handlers/BlockStats/BlockStats.vue'
import { NewBlockSubscription } from '@app/modules/blocks/NewBlockSubscription/newBlockSubscription.mixin'
import HomeGasPriceChart from '@app/modules/charts/handlers/HomeGasPriceChart.vue'
import HomeTxChart from '@app/modules/charts/handlers/HomeTxChart.vue'
import RecentBlocks from '@app/modules/blocks/handlers/RecentBlocks/RecentBlocks.vue'
import HomeTxs from '@app/modules/txs/handlers/BlockTxs/BlockTxs.vue'
import { Component, Mixins } from 'vue-property-decorator'
import { fromWei, toBN } from 'web3-utils'
import { ErrorMessageBlock } from '@app/modules/blocks/models/ErrorMessagesForBlock'
import mqtt from 'mqtt'

const MAX_ITEMS = 10

@Component({
    components: {
        AppBreadCrumbs,
        AppMessage,
        BlockStats,
        RecentBlocks,
        HomeTxs,
        HomeGasPriceChart,
        HomeTxChart,
        SearchDetails
    }
})
export default class PageHome extends Mixins(NewBlockSubscription) {
    /*
    ===================================================================================
      Initial Data
    ===================================================================================
    */
    errorMessages: ErrorMessageBlock[] = []
    topicBlock: any[] = []
    topicTransaction: any[] = []
    /*
    ===================================================================================
      Computed
    ===================================================================================
    */
    get maxItems(): number {
        return MAX_ITEMS
    }

    /*
    ===================================================================================
      Methods
    ===================================================================================
    */
    /**
     * Sets error messages if any
     * @param hasError {Boolean}
     * @param message {ErrorMessageBlock}
     */
    setError(hasError: boolean, message: ErrorMessageBlock): void {
        if (hasError) {
            if (!this.errorMessages.includes(message)) {
                this.errorMessages.push(message)
            }
        } else {
            if (this.errorMessages.length > 0) {
                const index = this.errorMessages.indexOf(message)
                if (index > -1) {
                    this.errorMessages.splice(index, 1)
                }
            }
        }
    }
    subscribeTopic(): void {
        // Start subscribe topic
        const client = mqtt.connect('wss://mqtt.acscan.net:8084/mqtt', { username: 'frontend', password: 'DDgg8WHv8YLdp9t' })
        client.on('connect', () => {
            // Connected mqtt success
            client.subscribe(['movoscan/block', 'movoscan/transaction'], err => {})
        })
        client.on('message', (topic, payload, packet) => {
            const storyDetail = JSON.parse(payload.toString())
            if (topic === 'movoscan/block') {
                const topicBlock: any[] = this.topicBlock
                if (topicBlock.length >= 20) {
                    topicBlock.splice(0, 1)
                }
                this.topicBlock = [...topicBlock, storyDetail]
            }
            if (topic === 'movoscan/transaction') {
                const topicTransaction: any[] = this.topicTransaction
                if (topicTransaction.length >= 20) {
                    topicTransaction.splice(0, 1)
                }
                this.topicTransaction = [...topicTransaction, storyDetail]
            }
        })
    }

    mounted() {
        this.subscribeTopic()
    }
    beforeUnmount() {
        console.log('unmounted')
    }
}
</script>

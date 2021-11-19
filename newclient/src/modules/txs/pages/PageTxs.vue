<template>
    <v-container grid-list-lg class="mt-0">
        <!-- <app-bread-crumbs :new-items="crumbs" /> -->
        <app-message :messages="errorMessages" />
        <v-layout row justify-center my-4>
            <v-flex xs12>
                <!-- <block-txs :max-items="maxItems" page-type="txs" @errorTxs="setError" /> -->
                <block-txs :max-items="maxItems" :topic-transaction="topicTransaction" page-type="txs" @errorTxs="setError" />
            </v-flex>
        </v-layout>
    </v-container>
</template>

<script lang="ts">
import AppBreadCrumbs from '@app/core/components/ui/AppBreadCrumbs.vue'
import AppMessage from '@app/core/components/ui/AppMessage.vue'
import BlockTxs from '@app/modules/txs/handlers/BlockTxs/BlockTxs.vue'
import { Crumb } from '@app/core/components/props'
import { Vue, Component } from 'vue-property-decorator'
import { ErrorMessageBlock } from '@app/modules/blocks/models/ErrorMessagesForBlock'
import mqtt from 'mqtt'

const MAX_ITEMS = 50

@Component({
    components: {
        AppBreadCrumbs,
        AppMessage,
        BlockTxs
    }
})
export default class PageTxs extends Vue {
    /*
    ===================================================================================
      Initial Data
    ===================================================================================
    */
    errorMessages: ErrorMessageBlock[] = []
    topicTransaction: any[] = []
    /*
    ===================================================================================
      Lifecycle
    ===================================================================================
    */

    mounted() {
        window.scrollTo(0, 0)
        this.subscribeTopic()
    }

    /*
    ===================================================================================
      Computed Values
    ===================================================================================
    */

    get crumbs(): Crumb[] {
        return [
            {
                text: this.$t('tx.mined-tx')
            }
        ]
    }

    get maxItems(): number {
        return MAX_ITEMS
    }
    /*
    ===================================================================================
      Methods
    ===================================================================================
    */
    /**
     * Sets error if any
     * @param hasError {Boolean}
     * @param message {ErrorMessageToken}
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
    subscribeTopic() : void{
        // Start subscribe topic
        const client = mqtt.connect('wss://mqtt.acscan.net:8084/mqtt', {username: 'frontend', password: 'DDgg8WHv8YLdp9t'})
        client.on('connect', () => {
            // Connected mqtt success
            client.subscribe('cmcscan/transaction', (err) => {})
        });
        client.on('message', (topic, payload, packet) => {
            let storyDetail = JSON.parse(payload.toString())
            let topicTransaction: any[] = this.topicTransaction
            if (topicTransaction.length >= 20) {
                topicTransaction.splice(0,1)
            }
            this.topicTransaction = [...topicTransaction, storyDetail]
        });
    }
}
</script>

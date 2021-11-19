<template>
    <div>
        <div class="row">
            <div class="col-sm-4">
                <div class="media align-items-sm-center mr-4 mb-1 mb-sm-0">
                    <div class="d-none d-sm-flex mr-2">
                        <span class="btn btn-icon btn-soft-secondary rounded-circle"><span class="btn-icon__inner text-dark">Tx</span></span>
                    </div>
                    <div class="media-body">
                        <span class="d-inline-block d-sm-none mr-1">TX#</span
                        ><a class="hash-tag hash-tag--xs hash-tag-xs-down--md text-truncate" :href="`/tx/${transaction.hash}`"
                            ><app-transform-hash :hash="transaction.hash" /></a
                        ><span class="d-none d-sm-block small text-secondary">
                            <app-time-ago :timestamp="transaction.timestamp" />
                        </span>
                    </div>
                </div>
            </div>
            <div class="col-sm-8">
                <div class="d-sm-flex justify-content-between">
                    <div class="text-nowrap mr-4 mb-1 mb-sm-0">
                        <span>
                            {{ $t('tx.from') }}
                            <a class="hash-tag text-truncate" :href="`/address/${transaction.from}`">
                                <app-transform-hash :hash="transaction.from | toChecksum" />
                            </a>
                        </span>
                        <br />
                        <span>
                            <template v-if="transaction.to && transaction.to !== ''">
                                {{ $t('tx.to') }}
                                <a class="hash-tag text-truncate" :href="`/address/${transaction.to}`">
                                    <app-transform-hash :hash="transaction.to | toChecksum" />
                                </a>
                            </template>
                            <template v-else>
                                {{ $t('contract.creation') }}
                            </template>
                        </span>
                    </div>
                    <div>
                        <span
                            class="u-label u-label--xs u-label--badge-in u-label--secondary text-center text-nowrap"
                            data-toggle="tooltip"
                            title=""
                            data-original-title="Amount"
                            >{{ transaction.value.value }} {{ $t(`common.${transaction.value.unit}`) }}</span
                        >
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script lang="ts">
import AppTransformHash from '@app/core/components/ui/AppTransformHash.vue'
import { Component, Mixins, Prop } from 'vue-property-decorator'
import AppTimeAgo from '@app/core/components/ui/AppTimeAgo.vue'
import AppTooltip from '@app/core/components/ui/AppTooltip.vue'
import { NumberFormatMixin } from '@app/core/components/mixins/number-format.mixin'
import { FormattedNumber } from '@app/core/helper/number-format-helper'
import { Tx } from './props'
import { TxSummary_transfers as TransferType } from '@app/modules/txs/handlers/BlockTxs/apolloTypes/TxSummary'
import { TxSummary_transfers_transfer as TransferObj } from '@app/modules/txs/handlers/BlockTxs/apolloTypes/TxSummary'
import { pendingTx_pendingTransaction as PendingTx } from '@app/modules/txs/handlers/PendingTxs/apolloTypes/pendingTx'

import BN from 'bignumber.js'
import { log } from 'console'

@Component({
    components: {
        AppTooltip,
        AppTimeAgo,
        AppTransformHash
    }
})
export default class TableTxsRow extends Mixins(NumberFormatMixin) {
    /*
    ===================================================================================
      Props
    ===================================================================================
    */
    // TransferType
    @Prop(Object) tx!: any
    @Prop({ type: Boolean, default: false }) isPending!: boolean

    /*
    ===================================================================================
      Computed
    ===================================================================================
    */

    get txStatusClass(): string {
        return this.transferObj.status ? 'tx-status-sucess table-row-mobile' : 'tx-status-fail table-row-mobile'
    }

    get transferObj(): TransferObj {
        return this.tx ? this.tx.transfer : { transactionHash: '', block: 0, from: '', to: '', timestamp: 0, txFee: '', status: false, __typename: 'Transfer' }
    }

    get transaction(): Tx {
        const tx = this.isPending ? this.tx : this.transferObj
        if (tx.transfer) {
            return {
                isMined: this.isPending ? tx.transfer.status : false,
                hash: tx.transfer.transactionHash,
                block: this.formatNumber(tx.transfer.block),
                from: tx.transfer.from,
                to: tx.transfer.to,
                timestamp: new Date(tx.transfer.timestamp * 1e3),
                fee: this.formatNonVariableCmcValue(new BN(tx.transfer.txFee)),
                value: this.formatNonVariableCmcValue(new BN(tx ? tx.value : '')),
                status: tx.transfer.status != null ? tx.transfer.status : false
            }
        } else {
            return {
                isMined: this.isPending ? tx['isMined'] : false,
                hash: tx['transactionHash'],
                block: this.formatNumber(tx['block']),
                from: tx['from'],
                to: tx['to'],
                timestamp: new Date(tx['timestamp'] * 1e3),
                fee: this.formatNonVariableCmcValue(new BN(tx['txFee'])),
                value: this.formatNonVariableCmcValue(new BN(this.tx ? this.tx.value : '')),
                status: tx['status'] != null ? tx['status'] : false
            }
        }
    }

    get isSmall(): boolean {
        return this.$vuetify.breakpoint.name === 'sm'
    }
}
</script>

<style scoped lang="css">
.table-row-mobile {
    border: 1px solid #b4bfd2;
}

p {
    margin-bottom: 0px;
    padding-bottom: 0px;
}

.tx-status-fail {
    border-left: 2px solid #fe1377;
}

.tx-status-sucess {
    border-left: 2px solid #40ce9c;
}
.tx-string {
    min-width: 3em;
}

.tx-status {
    min-width: 60px;
}

.blinking {
    animation: blinker 2s linear infinite;
}
@keyframes blinker {
    50% {
        opacity: 0;
    }
}
</style>

<template>
    <div>
        <div class="row">
            <div class="col-sm-4">
                <div class="media align-items-sm-center mr-4 mb-1 mb-sm-0">
                    <div class="d-none d-sm-flex mr-2">
                        <span class="btn btn-icon btn-soft-secondary"><span class="btn-icon__inner text-dark">Bk</span></span>
                    </div>
                    <div class="media-body">
                        <span class="d-inline-block d-sm-none">Block</span>
                        <router-link :to="`/block/number/${block.number}`">{{ $t('block.number') }} {{ _block.number }}</router-link>
                    </div>
                </div>
            </div>
            <div class="col-sm-8">
                <div class="d-flex justify-content-between">
                    <div class="text-nowrap">
                        <span class="d-block mb-1 mb-sm-0"
                            >Validated By
                            <a class="hash-tag text-truncate" :href="`/address/${_block.miner}`">{{ _block.miner }}</a>
                        </span>
                        <span data-toggle="tooltip" :title="_block.txFail > 0 ? txTooltipText : ''">
                            {{ _block.totalTx }} {{ $tc('tx.name-short', 2) }}
                            <!-- <app-tooltip v-if="_block.txFail > 0" :text="txTooltipText" /> -->
                        </span>
                        <span class="small text-secondary">
                            {{ $t('common.age') }}: <app-time-ago class="small text-secondary" :timestamp="_block.timestamp" />
                        </span>
                        <span class="d-inline-block d-sm-none">
                            <span
                                class="u-label u-label--xs u-label--badge-in u-label--secondary text-center text-nowrap"
                                data-toggle="tooltip"
                                title="Block Reward"
                                >{{ _block.rewards.value }}
                            </span>
                        </span>
                    </div>
                    <div class="d-none d-sm-block">
                        <span
                            class="u-label u-label--xs u-label--badge-in u-label--secondary text-center text-nowrap"
                            data-toggle="tooltip"
                            :title="_block.rewards.tooltipText ? `${_block.rewards.tooltipText} ${$t('common.movo')}` : ''"
                            >{{ _block.rewards.value }}
                        </span>
                    </div>
                </div>
            </div>
        </div>
        <hr class="hr-space" />
    </div>
</template>

<script lang="ts">
import { Component, Prop, Mixins } from 'vue-property-decorator'
import AppTransformHash from '@app/core/components/ui/AppTransformHash.vue'
import { NumberFormatMixin } from '@app/core/components/mixins/number-format.mixin'
import AppTimeAgo from '@app/core/components/ui/AppTimeAgo.vue'

import BN from 'bignumber.js'

import AppTooltip from '@app/core/components/ui/AppTooltip.vue'

@Component({
    components: {
        AppTooltip,
        AppTransformHash,
        AppTimeAgo
    }
})
export default class TableBlocksRow extends Mixins(NumberFormatMixin) {
    /*
  ===================================================================================
    Props
  ===================================================================================
  */

    @Prop(Object) block!: any
    @Prop({ type: String, default: 'home' }) pageType!: string

    /*
  ===================================================================================
   Computed
  ===================================================================================
  */

    get _block(): any {
        return {
            number: this.formatNumber(this.block.number),
            miner: this.block.miner,
            rewards: this.formatNonVariableEthValue(new BN(this.block.rewards.total)),
            timestamp: new Date(this.block.timestamp * 1e3),
            totalTx: this.formatNumber(this.block.txCount),
            txFail: this.formatNumber(this.block.txFail),
            txSuccess: this.formatNumber(this.block.txCount - this.block.txFail)
        }
    }
    get txTooltipText(): string {
        return `${this._block.txSuccess} ${this.$tc('tx.success', this.sucessTransalate())}, ${this._block.txFail} ${this.$tc(
            'tx.failed',
            this.failedTranslate()
        )}`
    }
    /*
  ===================================================================================
   Methods
  ===================================================================================
  */
    /**
     * Called when translation is success
     * @returns {Number}
     */
    sucessTransalate(): number {
        return this._block && this._block.txSuccess > 1 ? 2 : 1
    }
    /**
     * Called when translation is fails
     * @returns {Number}
     */
    failedTranslate(): number {
        return this.block && this._block.txFail > 1 ? 2 : 1
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
.arrow {
    position: relative;
}

.line {
    border-left: 1px solid #b4bfd2;
    border-bottom: 1px solid #b4bfd2;
    height: 50px;
    width: 105%;
    position: absolute;
    margin-left: 2px;
    margin-bottom: 10px;
}
</style>

<template>
    <tr>
        <td>
            <router-link :to="`/block/number/${block.number}`">{{ $t('block.number') }} {{ _block.number }}</router-link>
        </td>
        <td style="" class="showAge">
            <app-time-ago class="small text-secondary" :timestamp="_block.timestamp" />
            <!-- <span rel="tooltip" data-toggle="tooltip" data-placement="bottom" title="2021-10-03 13:41:37">5 secs ago</span> -->
        </td>
        <td>
            <span data-toggle="tooltip" :title="_block.txFail > 0 ? txTooltipText : ''"> {{ _block.totalTx }} {{ $tc('tx.name-short', 2) }} </span>
        </td>
        <td>
            <a class="hash-tag text-truncate" data-toggle="tooltip" :title="_block.miner" :href="`/address/${block.miner}`">{{ _block.miner }}</a>
        </td>
        <td>
            <span :title="_block.rewards.tooltipText ? `${_block.rewards.tooltipText} ${$t('common.movo')}` : ''">
                {{ _block.rewards.value }}
            </span>
        </td>
    </tr>
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

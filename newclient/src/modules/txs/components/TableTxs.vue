<template>
    <div>
        <template v-if="pageType == 'home'">
            <template v-if="!isLoading">
                <template v-if="displayData && displayData.length > 0">
                    <template v-for="(tx, index) in displayData">
                        <table-txs-row :key="index" :tx="tx" :is-pending="pending" />
                        <hr :key="index + 'k'" class="hr-space" />
                    </template>
                </template>
                <template v-else>
                    <p class="text-center h5">{{ $t('tx.no-result') }}</p>
                </template>
            </template>
            <template v-if="isLoading" xs12>
                <div v-for="i in maxItems" :key="i">
                    <app-table-row-loading />
                </div>
            </template>
        </template>
        <template v-else>
            <!-- <div v-if="0" class="table-responsive mb-2 mb-md-0">
                <table class="table table-hover">
                    <thead class="thead-light">
                        <tr>
                            <th v-if="!pending" scope="col">{{ $t('block.number') }}</th>
                            <th scope="col">{{ $tc('tx.hash', 1) }}</th>
                            <th scope="col">{{ $t('common.amount') }}</th>
                            <th scope="col">{{ $t('common.age') }}</th>
                            <th scope="col">{{ pending ? $t('tx.estimated-fee') : $tc('tx.fee', 1) }}</th>
                            <th v-if="!pending" scope="col">{{ $t('tx.status') }}</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-if="isLoading">
                            <td :colspan="!pending ? 6 : 4">
                                <app-table-row-loading />
                            </td>
                        </tr>
                        <template v-else>
                            <template v-if="displayData && displayData.length > 0">
                                <template v-for="(tx, index) in displayData">
                                    <table-txs-row :key="index" :tx="tx" :is-pending="pending" />
                                </template>
                            </template>
                            <template v-else>
                                <td :colspan="!pending ? 6 : 4">
                                    <p class="h5 text-center">{{ $t('tx.no-result') }}</p>
                                </td>
                            </template>
                        </template>
                    </tbody>
                </table>
            </div> -->
            <template v-if="1">
                <!-- <app-error :has-error="hasMessage" :message="tableMessage" class="mb-4" /> -->

                <!--
                =====================================================================================
                TABLE HEADER
                =====================================================================================
                -->
                <v-layout pl-2 pr-2>
                    <v-flex hidden-sm-and-down sm12>
                        <v-card color="info" flat class="white--text" height="40px">
                            <slot name="header">
                                <v-layout align-center justify-start row fill-height pl-3 pr-2>
                                    <v-flex v-if="!pending" sm2 lg1>
                                        <h5>{{ $t('block.number') }}</h5>
                                    </v-flex>
                                    <v-flex sm7 md5>
                                        <h5>{{ $tc('tx.hash', 1) }}</h5>
                                    </v-flex>
                                    <v-flex sm3 lg2>
                                        <h5 class="pl-3">{{ $t('common.amount') }}</h5>
                                    </v-flex>
                                    <v-flex hidden-sm-and-down md2>
                                        <h5 class="pl-2">{{ $t('common.age') }}</h5>
                                    </v-flex>
                                    <v-flex hidden-md-and-down lg2>
                                        <h5>{{ pending ? $t('tx.estimated-fee') : $tc('tx.fee', 1) }}</h5>
                                    </v-flex>
                                    <div v-if="!pending">
                                        <h5 class="tx-status text-xs-center">{{ $t('tx.status') }}</h5>
                                    </div>
                                </v-layout>
                            </slot>
                        </v-card>
                    </v-flex>
                </v-layout>
                <!--
                =====================================================================================
                TABLE BODY
                =====================================================================================
                -->
                <div>
                    <v-card v-if="!hasMessage" :style="getStyle" flat class="scroll-y pa-2" style="overflow-x: hidden">
                        <v-layout column fill-height class="mb-1">
                            <v-flex v-if="!isLoading" xs12>
                                <slot name="rows">
                                    <v-card v-for="(tx, index) in displayData" :key="index" class="transparent" flat>
                                        <table-txs-row-in-page :tx="tx" :is-pending="pending" />
                                    </v-card>
                                </slot>
                            </v-flex>
                            <v-flex v-else xs12>
                                <div v-for="i in maxItems" :key="i">
                                    <app-table-row-loading />
                                </div>
                            </v-flex>
                        </v-layout>
                    </v-card>
                    <v-card v-else flat>
                        <v-card-title>{{ tableMessage }} </v-card-title>
                        <v-divider class="ma-2 hidden-sm-and-down" />
                    </v-card>
                </div>
            </template>
        </template>
    </div>
</template>

<script lang="ts">
import TableTxsRow from '@app/modules/txs/components/TableTxsRow.vue'
import TableTxsRowInPage from '@app/modules/txs/components/TableTxsRowInPage.vue'
import AppTableRowLoading from '@app/core/components/ui/AppTableRowLoading.vue'
import { Component, Prop, Vue } from 'vue-property-decorator'
import { Footnote } from '@app/core/components/props'
const SCROLLVIEW = 'max-height: 450px'

/*
  DEV NOTES:
  - add App Error implementation on error
  - add App footnotes
*/

@Component({
    components: {
        TableTxsRow,
        TableTxsRowInPage,
        AppTableRowLoading
    }
})
export default class TableTxs extends Vue {
    /*
    ===================================================================================
      Props
    ===================================================================================
    */

    @Prop(Array) txsData!: any[]
    @Prop(Boolean) isLoading!: boolean
    @Prop(Number) maxItems!: number
    @Prop(Number) index!: number
    @Prop({ type: String, default: '' }) address!: string
    @Prop({ type: String, default: '' }) tableMessage!: string
    @Prop({ type: Boolean, default: false }) isScrollView!: boolean
    @Prop({ type: Boolean, default: false }) pending!: boolean
    @Prop({ type: String, default: 'home' }) pageType!: string

    /*
    ===================================================================================
      Computed Values
    ===================================================================================
    */

    get isAddress(): boolean {
        return this.address === ''
    }
    get hasMessage(): boolean {
        return this.tableMessage != ''
    }

    get getStyle(): string {
        return this.isScrollView ? SCROLLVIEW : ''
    }

    get displayData(): any[] {
        if (this.txsData) {
            const start = this.index * this.maxItems
            const end = start + this.maxItems > this.txsData.length ? this.txsData.length : start + this.maxItems
            return this.txsData.slice(start, end)
        }
        return []
    }
}
</script>
<style scoped lang="css">
.tx-filter-select-container {
    border: solid 1px #efefef;
    padding-top: 1px;
}
.tx-status {
    min-width: 60px;
}
</style>

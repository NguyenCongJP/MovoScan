<template>
    <footer class="bg-dark py-4" style="background-image: url(/background.png); background-repeat: repeat">
        <div class="container">
            <div class="row justify-content-sm-between align-items-center">
                <div class="col-sm-6 mb-3 mb-sm-0">
                    <div class="">
                        <img src="/Logo_MOVO-scan white.png" alt="Binance Logo" style="height: 50px; width: auto" />
                        <!-- <span class="h5 text-white mb-0 ml-3">Powered by Binance Chain</span> -->
                    </div>
                </div>
                <div class="col-sm-6 align-self-bottom">
                    <div class="justify-content-sm-end align-items-center mb-4" style="display: flex">
                        <span class="d-none d-md-inline-block">
                            <button type="button" class="btn btn-xss btn-soft-light text-nowrap d-flex align-items-center mr-2" @click="addNetwork">
                                <img class="mr-1" style="width: 15px" src="https://testnet.bscscan.com/images/svg/brands/metamask.svg" alt="Metamask" /> Add MOVO
                                Chain Testnet <i v-if="loadingAddNetwork" class="ml-2 fas fa-circle-notch fa-spin"></i>
                            </button>
                        </span>
                        <!-- <a class="btn btn-xss btn-soft-light mr-2" href="/settings"> <i class="fa fa-cogs mr-1"></i>Preferences </a>
                        <button id="darkModaBtn" type="button" data-toggle="tooltip" data-title="Day/Night Mode" class="btn btn-sm btn-icon btn-soft-light">
                            <i id="darkModaBtnIcon" Class="fa fa-moon"></i>
                        </button> -->
                    </div>
                </div>
            </div>
            <!-- <hr class="opacity-md" /> -->
            <div class="row justify-content-between align-items-center font-size-1">
                <div class="col-md-6 mb-2 mb-md-0 d-flex">
                    <p class="mb-0 text-white">
                        MOVOscan © 2021 (MOVO) <span class="mx-1">|</span> Built by the same team behind
                        <a class="text-primary" href="https://movoscan.io/" target="_blank"><b>movoscan</b></a> <span class="mx-1">|</span>
                        <a class="text-white-70" href="/">Donate </a> <i class="fas fa-heart text-danger"></i>
                    </p>
                </div>
                <div class="col-md-6 text-md-right">
                    <ul class="list-inline mb-0">
                        <li class="list-inline-item">
                            <a
                                class="btn btn-sm btn-icon btn-soft-light btn-pill"
                                href="/instagram"
                                data-toggle="tooltip"
                                data-placement="top"
                                title="Contact Us"
                            >
                                <i class="fab fa-instagram btn-icon__inner"></i>
                            </a>
                        </li>
                        <li class="list-inline-item">
                            <a
                                class="btn btn-sm btn-icon btn-soft-light btn-pill"
                                href="/facebook"
                                data-toggle="tooltip"
                                data-placement="top"
                                title="Contact Us"
                            >
                                <i class="fab fa-facebook-f btn-icon__inner"></i>
                            </a>
                        </li>
                        <li class="list-inline-item">
                            <a
                                class="btn btn-sm btn-icon btn-soft-light btn-pill"
                                href="/youtube"
                                data-toggle="tooltip"
                                data-placement="top"
                                title="Contact Us"
                            >
                                <i class="fab fa-youtube btn-icon__inner"></i>
                            </a>
                        </li>
                        <li class="list-inline-item">
                            <a
                                class="btn btn-sm btn-icon btn-soft-light btn-pill"
                                href="https://www.linkedin.com/in/movoscan/"
                                data-toggle="tooltip"
                                data-placement="top"
                                title="LinkedIn"
                            >
                                <i class="fab fa-linkedin-in btn-icon__inner"></i>
                            </a>
                        </li>
                        <li class="list-inline-item">
                            <a
                                class="btn btn-sm btn-icon btn-soft-light btn-pill"
                                href="https://twitter.com/MovoScan"
                                rel="nofollow noopener"
                                target="_blank"
                                data-toggle="tooltip"
                                data-placement="top"
                                title="Twitter"
                            >
                                <i class="fab fa-twitter btn-icon__inner"></i>
                            </a>
                        </li>
                        <li class="list-inline-item">
                            <a
                                class="btn btn-sm btn-icon btn-soft-light btn-pill"
                                href="https://medium.com/@movoscan"
                                rel="nofollow noopener"
                                target="_blank"
                                data-toggle="tooltip"
                                data-placement="top"
                                title="Medium"
                            >
                                <i class="fab fa-medium-m btn-icon__inner"></i>
                            </a>
                        </li>
                    </ul>
                </div>
            </div>
        </div>
    </footer>
</template>

<script lang="ts">
import AppCopyToClip from '@app/core/components/ui/AppCopyToClip.vue'
import AppLanguage from '@app/core/components/ui/AppLanguage.vue'
import configs from '@app/configs'
import { Vue, Component, Prop } from 'vue-property-decorator'
import { Vuetify } from 'vuetify'

@Component({
    components: {
        AppCopyToClip,
        AppLanguage
    }
})
export default class TheFooter extends Vue {
    /*
  ===================================================================================
    Initial Data
  ===================================================================================
  */

    btcAddr: string = '1DECAF2uSpFTP4L1fAHR8GCLrPqdwdLse9'
    ethAddr: string = '0xDECAF9CD2367cdbb726E904cD6397eDFcAe6068D'
    version: string = configs.VERSION || ''
    loadingAddNetwork: boolean = false

    /*
  ===================================================================================
    Computed
  ===================================================================================
  */
    get flexClass(): string {
        return this.$vuetify.breakpoint.name === 'sm' || this.$vuetify.breakpoint.name === 'xs' ? 'pa-1' : 'pa-3'
    }
    get bitcoinUrl(): string {
        return `https://www.blockchain.com/btc/address/${this.btcAddr}`
    }
    get ethPath(): object {
        return { name: 'address', params: { addressRef: this.ethAddr } }
    }

    openMewLink(): void {
        window.open('https://www.myetherwallet.com/', '_blank')
    }

    addNetwork(): void {
        console.log('test pust')
        this.loadingAddNetwork = true
        const params = [
            {
                chainId: '0x0027', //'39',
                chainName: 'MOVO Smart Chain Testnet',
                nativeCurrency: {
                    name: 'tMOVO',
                    symbol: 'tMOVO',
                    decimals: 18
                },
                rpcUrls: ['https://vayne.movochain.network/'],
                blockExplorerUrls: ['https://movoscan.com']
            }
        ]

        if (typeof window['ethereum'] !== 'undefined') {
            window['ethereum']
                .request({ method: 'wallet_addEthereumChain', params })
                .then(() => {
                    this.loadingAddNetwork = false
                    console.log('success')
                })
                .catch(error => {
                    this.loadingAddNetwork = false
                    console.log('Error', error.message)
                })
        }
    }
}
</script>

<style scoped lang="css">
.donation-btn {
    width: 200px;
}
.asset-icon {
    font-size: 14px;
}
</style>

<template>
    <header style="background-color: #fff; box-shadow: 0 1px 10px rgb(151 164 175 / 10%)">
        <nav class="container cmcnavbar">
            <div class="cmcnavbar_destop">
                <div style="display: flex; height: 70px" class="align-items-center w-100">
                    <div class="align-items-center justify-content-between w-100" style="display: flex; height: 100%">
                        <a href="/" class="d-inline-block">
                            <img id="logo-header" style="height: 40px; width: auto" src="/Logo_CMC-scan_3_512px.png" alt="Cmc Logo" />
                        </a>
                        <ul class="align-items-center cmcnavbar_menu" style="display: flex">
                            <template v-for="(item, index) in items">
                                <li :key="index" class="cmcnavbar_link active" :class="{ cmcnavbar_link_sub: item.links && item.links.length > 0 }">
                                    <i v-if="item.header.text === 'Sign In'" class="fas fa-user-circle fa-lg mr-1 signTab" style="color: gray"></i>
                                    <div>
                                        <img v-if="item.header.text === ''" style="height: 22px; width: auto" src="/favicon_cmc.png" alt="Movo Logo">
                                        
                                    </div>
                                    
                                    <a :href="item.header.routerLink">
                                        {{ item.header.text }}
                                        <i v-if="item.links && item.links.length > 0 && item.header.text !== ''" style="font-size: 10px" class="fas fa-chevron-down"></i>
                                    </a>
                                    <ul v-if="item.links && item.links.length > 0  && item.links.length < -1" class="cmcnavbar_sub cmcnavbar_sub2">
                                        <template v-for="(sub, sind) in item.links">
                                            <li :key="sind">
                                                <a :href="sub.routerLink">{{ sub.text }}</a>
                                            </li>
                                        </template>
                                    </ul>
                                </li>
                            </template>
                        </ul>
                    </div>
                </div>
                <div v-if="$route.path != '/'" class="row justify-content-end mt-2">
                    <div class="col-sm-12 col-md-7">
                        <search-details />
                    </div>
                </div>
            </div>
            <div class="cmcnavbar_mobile justify-content-between align-items-center w-100">
                <a href="/" class="d-inline-block">
                    <img id="logo-header" style="height: 40px; width: auto" src="/logo_cmc.png" alt="Binance Logo" />
                </a>
                <v-icon @click="drawer = !drawer">fas fa-bars</v-icon>
            </div>
            <v-navigation-drawer v-model="drawer" :mini-variant="mini" class="white elevation-3 p-3" app mini-variant-width="100px">
                <v-list>
                    <template v-for="(item, index) in items">
                        <v-list-group v-if="item.links && item.links.length > 0" :key="index" no-action>
                            <template #activator>
                                <v-list-tile>
                                    <v-list-tile-content>
                                        <a class="text-dark" :href="item.header.routerLink">
                                            {{ item.header.text }}
                                        </a>
                                    </v-list-tile-content>
                                </v-list-tile>
                            </template>

                            <v-list-tile v-for="(subItem, sind) in item.links" :key="sind">
                                <v-list-tile-content>
                                    <a class="text-dark" :href="subItem.routerLink">
                                        {{ subItem.text }}
                                    </a>
                                </v-list-tile-content>
                            </v-list-tile>
                        </v-list-group>
                        <v-list-tile v-else :key="index">
                            <v-list-tile-content>
                                <a class="text-dark" :href="item.header.routerLink">
                                    {{ item.header.text }}
                                </a>
                            </v-list-tile-content>
                        </v-list-tile>
                    </template>
                </v-list>
            </v-navigation-drawer>
        </nav>
    </header>
</template>

<script lang="ts">
import SearchDetails from '@app/modules/search/handlers/SearchDetails.vue'
import { NavMenuEntry } from '@app/core/components/props'
import { Vue, Component, Prop, Watch } from 'vue-property-decorator'

@Component({
    components: {
        SearchDetails
    }
})
export default class TheNavigationDrawer extends Vue {
    /*
  ===================================================================================
    Initial Data
  ===================================================================================
  */

    supportEmail = 'support@ethvm.com'
    drawer: boolean = false
    active = 0
    sublink = null
    mini: boolean = false
    showSearchMobile: boolean = false

    /*
  ===================================================================================
    LifeCycle
  ===================================================================================
  */

    created() {
        if (!this.showMobile) {
            // this.drawer = true
        }
    }

    /*
  ===================================================================================
    Computed
  ===================================================================================
  */

    get showMobile(): boolean {
        return this.$vuetify.breakpoint.name === 'md' || this.$vuetify.breakpoint.name === 'sm' || this.$vuetify.breakpoint.name === 'xs'
    }

    get headerColor(): string {
        return this.showMobile ? 'primary' : 'white'
    }

    get items(): NavMenuEntry[] {
        return [
            {
                header: {
                    icon: 'fa fa-home',
                    text: this.$i18n.t('home'),
                    routerLink: '/'
                }
            },
            {
                header: {
                    icon: 'fa fa-cubes',
                    text: this.$i18n.tc('block.name', 2),
                    routerLink: '/blocks'
                },
                links: [
                    {
                        text: this.$i18n.t('btn.view-all'),
                        routerLink: '/blocks',
                        name: 'blocks'
                    },
                    {
                        text: this.$i18n.tc('uncle.name', 2),
                        routerLink: '/uncles',
                        name: 'uncles'
                    }
                ]
            },
            {
                header: {
                    text: this.$i18n.tc('tx.name', 2),
                    icon: 'fas fa-exchange-alt',
                    routerLink: '/txs'
                },
                links: [
                    {
                        text: this.$i18n.t('tx.mined-tx'),
                        routerLink: '/txs',
                        name: 'transactions'
                    },
                    {
                        text: this.$i18n.tc('tx.pending', 2),
                        routerLink: '/pending-txs',
                        name: 'pending'
                    }
                ]
            },
            {
                header: {
                    text: this.$i18n.tc('token.name', 2),
                    icon: 'fab fa-ethereum',
                    routerLink: '/tokens'
                },
                links: [
                    {
                        text: this.$i18n.t('token.view-all'),
                        routerLink: '/tokens',
                        name: 'tokens'
                    },
                    {
                        text: this.$i18n.tc('token.favorite', 2),
                        routerLink: '/tokens/favorites',
                        name: 'fav_tokens'
                    }
                ]
            },
            // {
            //     header: {
            //         icon: 'fas fa-chart-pie',
            //         text: this.$i18n.t('charts.name'),
            //         routerLink: '/charts'
            //     }
            // },
            {
                header: {
                    icon: 'fas fa-heart',
                    text: this.$t('fav.title'),
                    routerLink: '/fav_addresses'
                }
            },
            {
                header: {
                    icon: 'fas fa-user-circle',
                    text: 'Sign In',
                    routerLink: '/login'
                }
            },
            {
                header: {
                    icon: 'fas fa-user-circle',
                    text: ''
                },
                links: [
                    {
                        text: 'Movo Mainnet',
                        routerLink: 'http://movoscan.com/',
                        name: 'mainnet'
                    },
                    {
                        text: 'Movo Testnet',
                        routerLink: 'http://testnet.movoscan.com/',
                        name: 'testnet'
                    }
                ]
            }
        ]
    }

    /*
  ===================================================================================
    Methods
  ===================================================================================
  */
    /**
     * Sets drawer based on vuetify breakpoints
     *
     */
    setDrawer(): void {
        if (!this.showMobile) {
            this.drawer = true
            this.mini = !this.mini
        } else {
            this.mini = false
            this.drawer = !this.drawer
        }
    }

    /**
     * Fetches the current route path
     * @returns {String} route name
     */

    getCurrentPath(): string {
        return this.$route.name ? this.$route.name : ''
    }
    /**
     * Checks whether passed name matches current path
     * @param name {String}
     * @returns {Boolean}
     */
    isCurrentLinkPath(name: string): boolean {
        return this.getCurrentPath() == name
    }

    /**
     * Checks if any links is the current path
     * @param links any
     * @returns {Boolean}
     */
    checkLinkPath(links: any[] = []): boolean {
        for (const link of links) {
            if (this.isCurrentLinkPath(link.name)) {
                return true
            }
        }
        return false
    }
    @Watch('showMobile')
    onShowMobileChange(newVal: boolean): void {
        if (newVal === false) {
            this.showSearchMobile = false
        }
    }
}
</script>

<style scoped>
.cmcnavbar {
    min-height: 60px;
}
.cmcnavbar_mobile {
    display: none;
    height: 100%;
    height: 60px;
}
.cmcnavbar_destop {
    display: block;
    height: 100%;
    /* height: 60px; */
}
.cmcnavbar_link {
    position: relative;
}

.cmcnavbar_menu {
    height: 100%;
}

.cmcnavbar_link {
    height: 100%;
    display: flex;
    align-items: center;
    padding: 0.8rem 1rem;
}

.cmcnavbar_link a {
    color: #6c757e;
    font-size: 14px;
    font-weight: 400;
}

.cmcnavbar_link_sub:hover .cmcnavbar_sub {
    display: block;
}
.cmcnavbar_sub {
    display: none;
    position: absolute;
    top: 55px;
    left: 0;
    background-color: #fff;
    z-index: 9;

    border-top: 0.1875rem solid #3498db;
    border-bottom-right-radius: 0.35rem;
    border-bottom-left-radius: 0.35rem;
    box-shadow: 0 8px 20px rgb(52 152 219 / 8%);
}

.cmcnavbar_link_sub .cmcnavbar_sub li {
    margin-left: 0px;
    padding: 8px 16px;
    white-space: nowrap;
}

.cmcnavbar_sub2 {
    left: auto;
    right: 0;
}

@media screen and (max-width: 859px) {
    .cmcnavbar_destop {
        display: none;
    }
    .cmcnavbar_mobile {
        display: flex;
    }
}

/*Custom Css */

.signTab::before {
    content: "";
    width: 1px;
    height: 20px;
    background: #6c757e;
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    left: 0;
}

.signTab::after {
    content: "";
    width: 1px;
    height: 20px;
    background: #6c757e;
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    right: 0;
}
</style>

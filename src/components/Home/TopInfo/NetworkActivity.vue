<template>
    <div id="network_statistics" class="card">
        <div class="header">
            <h2 class="meta_title">
                Avalanche Network Activity
                <!-- <TooltipHeading content="Key Avalanche stats" :color="'#2196f3'"></TooltipHeading> -->
            </h2>
        </div>
        <section class="stats one-column">
            <!-- <article class="meta">
                <router-link class="stat" to="/tx">
                    <p class="label">
                        24h Transactions
                        <TooltipMeta
                            content="Total number of state queries or modifications of all blockchains on Avalanche in the past 24 hours"
                            :color="'#2196f3'"
                        ></TooltipMeta>
                    </p>
                    <div v-if="assetsLoaded">
                        <p class="meta_val">
                            {{totalTransactions.toLocaleString()}}
                            <span class="unit">{{tpmText}} TPM</span>
                        </p>
                    </div>
                    <div v-else>
                        <v-progress-circular :size="16" :width="2" color="#E84970" indeterminate key="1"></v-progress-circular>
                    </div>
                </router-link>
            </article> -->
            <article class="meta">
                <router-link class="stat" to="/assets">
                    <p class="label">
                        24h Volume
                        <TooltipMeta
                            content="Total value of AVAX transferred on Avalanche in the past 24 hours"
                            :color="'#2196f3'"
                        ></TooltipMeta>
                    </p>
                    <div v-if="assetsLoaded">
                        <p class="meta_val">
                            {{ avaxVolume }}
                            <span class="unit">AVAX</span>
                        </p>
                    </div>
                    <div v-else>
                        <v-progress-circular
                            :size="16"
                            :width="2"
                            color="#E84970"
                            indeterminate
                            key="1"
                        ></v-progress-circular>
                    </div>
                </router-link>
            </article>
        </section>
        <section class="stats two-column">
            <article class="meta">
                <router-link class="stat" to="/validators">
                    <p class="label">
                        Validators
                        <TooltipMeta
                            content="Total number of nodes validating transactions on Avalanche"
                            :color="'#2196f3'"
                        ></TooltipMeta>
                    </p>
                    <div v-if="subnetsLoaded">
                        <p class="meta_val">
                            {{ validatorCount.toLocaleString() }}
                        </p>
                    </div>
                    <div v-else>
                        <v-progress-circular
                            :size="16"
                            :width="2"
                            color="#E84970"
                            indeterminate
                            key="1"
                        ></v-progress-circular>
                    </div>
                </router-link>
            </article>
            <article class="meta">
                <router-link class="stat" to="/validators">
                    <p class="label">
                        Total Staked
                        <TooltipMeta
                            content="Total value of AVAX locked to secure Avalanche"
                            :color="'#2196f3'"
                        ></TooltipMeta>
                    </p>
                    <div v-if="subnetsLoaded">
                        <p class="meta_val">
                            {{ totalStake }}
                            <span class="unit">AVAX</span>
                        </p>
                    </div>
                    <div v-else>
                        <v-progress-circular
                            :size="16"
                            :width="2"
                            color="#E84970"
                            indeterminate
                            key="1"
                        ></v-progress-circular>
                    </div>
                </router-link>
            </article>
            <article class="meta">
                <router-link class="stat" to="/blockchains">
                    <p class="label">
                        Blockchains
                        <TooltipMeta
                            content="Total number of blockchains on Avalanche"
                            :color="'#2196f3'"
                        ></TooltipMeta>
                    </p>
                    <div v-if="subnetsLoaded">
                        <p class="meta_val">
                            {{ totalBlockchains }}
                        </p>
                    </div>
                    <div v-else>
                        <v-progress-circular
                            :size="16"
                            :width="2"
                            color="#E84970"
                            indeterminate
                            key="1"
                        ></v-progress-circular>
                    </div>
                </router-link>
            </article>
            <article class="meta">
                <router-link class="stat" to="/subnets">
                    <p class="label">
                        Subnets
                        <TooltipMeta
                            content="Total number of subnets on Avalanche"
                            :color="'#2196f3'"
                        ></TooltipMeta>
                    </p>
                    <div v-if="subnetsLoaded">
                        <p class="meta_val">
                            {{ totalSubnets }}
                        </p>
                    </div>
                    <div v-else>
                        <v-progress-circular
                            :size="16"
                            :width="2"
                            color="#E84970"
                            indeterminate
                            key="1"
                        ></v-progress-circular>
                    </div>
                </router-link>
            </article>
            <article class="meta">
                <router-link class="stat" to="/subnets">
                    <p class="label">
                        Staking Ratio
                        <TooltipMeta
                            content="Percentage of AVAX locked to secure Avalanche out of total AVAX supply (360m)"
                            :color="'#2196f3'"
                        ></TooltipMeta>
                    </p>
                    <div v-if="subnetsLoaded">
                        <p class="meta_val">{{ percentStaked }} %</p>
                    </div>
                    <div v-else>
                        <v-progress-circular
                            :size="16"
                            :width="2"
                            color="#E84970"
                            indeterminate
                            key="1"
                        ></v-progress-circular>
                    </div>
                </router-link>
            </article>
            <article class="meta">
                <router-link class="stat" to="/subnets">
                    <p class="label">Annual Staking Reward</p>
                    <div v-if="subnetsLoaded">
                        <p class="meta_val">{{annualStakingRewardPercentage}}</p>
                    </div>
                    <div v-else>
                        <v-progress-circular
                            :size="16"
                            :width="2"
                            color="#E84970"
                            indeterminate
                            key="1"
                        ></v-progress-circular>
                    </div>
                </router-link>
            </article>
        </section>
    </div>
</template>
<script lang="ts">
import "reflect-metadata";
import { Vue, Component, Watch } from "vue-property-decorator";

import axios from "@/axios";
import { stringToBig, bigToDenomBig } from "@/helper";
import TooltipHeading from "../../misc/TooltipHeading.vue";
import TooltipMeta from "../TopInfo/TooltipMeta.vue";
import { AVAX_ID } from "@/store/index";
import { Asset } from "@/js/Asset";
import Big from "big.js";
import { TOTAL_AVAX_SUPPLY } from "@/store/modules/platform/platform";
import { avalanche } from "@/avalanche";
import { Defaults, getPreferredHRP, ONEAVAX } from "avalanche/dist/utils";
import { BN } from "avalanche/dist";

@Component({
    components: {
        TooltipHeading,
        TooltipMeta,
    },
})
export default class NetworkActivity extends Vue {
    volumeCache: Big = Big(0);
    totalTransactionsCache: number = 0;

    get assetsLoaded(): boolean {
        return this.$store.state.assetsLoaded;
    }

    get assetAggregatesLoaded(): boolean {
        return this.$store.state.assetAggregatesLoaded;
    }

    get subnetsLoaded(): boolean {
        return this.$store.state.Platform.subnetsLoaded;
    }

    @Watch("avaxVolume")
    onAvaxVolumeChanged(val: string) {
        this.saveCacheAvax();
    }

    @Watch("totalTransactions")
    ontotalTransactionsChanged(val: number) {
        this.saveCacheTotalTransactions();
    }

    created() {
        // Get 24h volume cache
        // TODO: remove when API is enhanced
        let volumeCacheJSON = localStorage.getItem("avaxCache");
        let volumeCache = Big(0);
        if (volumeCacheJSON) {
            let cache = JSON.parse(volumeCacheJSON);
            volumeCache = Big(cache.volume_day);
        }
        this.volumeCache = volumeCache;

        // Get totalTransactions cache
        // TODO: remove when API is enhanced
        let totalTransactionsCacheJSON = localStorage.getItem(
            "totalTransactions"
        );
        let totalTransactionsCache = 0;
        if (totalTransactionsCacheJSON) {
            let cache = JSON.parse(totalTransactionsCacheJSON);
            totalTransactionsCache = cache;
        }
        this.totalTransactionsCache = totalTransactionsCache;
    }

    saveCacheAvax() {
        let asset = this.avax;
        if (asset) {
            let volume_day = asset.volume_day.toString();
            let txCount_day = asset.txCount_day;
            let cache = {
                volume_day, // AVAX volume
                txCount_day, // AVAX count
            };
            localStorage.setItem("avaxCache", JSON.stringify(cache));
        }
    }

    saveCacheTotalTransactions() {
        let totalTransactions = this.totalTransactions;
        let cache = {
            totalTransactions, // count across all assets
        };
        localStorage.setItem(
            "totalTransactions",
            JSON.stringify(totalTransactions)
        );
    }

    // Data from Ortelius
    get tpmText(): string {
        let day = 60 * 24;
        let avg = this.totalTransactions / day;
        return avg > 1 ? avg.toFixed(0) : avg.toFixed(2);
    }

    get tpsText(): string {
        let day = 60 * 60 * 24;
        let avg = this.totalTransactions / day;
        return avg > 1 ? avg.toFixed(0) : avg.toFixed(2);
    }

    get assets() {
        return this.$store.state.assets;
    }

    get avax(): Asset | undefined {
        return this.assets[AVAX_ID];
    }

    get avaxVolume(): string {
        if (!this.avax) {
            return parseInt(this.volumeCache.toFixed(0)).toLocaleString();
        }
        return this.avax.isHistoryUpdated
            ? parseInt(this.avax.volume_day.toFixed(0)).toLocaleString()
            : parseInt(this.volumeCache.toFixed(0)).toLocaleString();
    }

    get totalTransactions(): number {
        return this.$store.state.assetAggregatesLoaded
            ? this.$store.getters.totalTransactions
            : this.totalTransactionsCache;
    }

    // Data from Avalanche-Go
    get totalStake(): string {
        let res = this.$store.getters["Platform/totalStake"];
        res = stringToBig(res.toString(), 9).toFixed(0);
        return parseInt(res).toLocaleString();
    }

    get validatorCount(): number {
        return this.$store.getters["Platform/totalValidators"];
    }

    get totalBlockchains(): number {
        return this.$store.getters["Platform/totalBlockchains"];
    }

    get totalSubnets(): number {
        return Object.keys(this.$store.state.Platform.subnets).length;
    }

    get percentStaked(): string {
        let totalStake = this.$store.getters["Platform/totalStake"];
        totalStake = bigToDenomBig(totalStake, 9);
        let percentStaked = totalStake.div(TOTAL_AVAX_SUPPLY).times(100);
        return percentStaked.toFixed(2);
    }

    get annualStakingRewardPercentage(): string {
        let networkID = avalanche.getNetworkID();
        
        //@ts-ignore
        let defaultValues = Defaults.network[networkID];
        if (!defaultValues) {
            console.error("Network default values not found.")
            return "TBD";
        }

        let maxConsumption: number = defaultValues.P.maxConsumption;
        let minConsumption: number = defaultValues.P.minConsumption;
        let avgReturn: number = (((maxConsumption + minConsumption) / 2) * 100);
        
        return `${avgReturn.toFixed(0)}%`;
    }

    get annualStakingReward(): BN {
        // console.log("=====================================================")
        // console.log("=====================================================")
        
        let totalStake: string = this.$store.getters["Platform/totalStake"].toFixed();
        let totalStake_BN: BN = new BN(totalStake);
        // console.log("totalStake_BN                  ", totalStake_BN.toString());

        let ONE_YEAR_SECONDS = 60 * 60 * 24 * 365;
        let TOTAL_AVAX_SUPPLY_BN = new BN(TOTAL_AVAX_SUPPLY.times(Math.pow(10, 9)).toFixed());
        // console.log("TOTAL_AVAX_SUPPLY_BN           ", TOTAL_AVAX_SUPPLY_BN.toString());
        
        let annualStakingReward:BN = this.calculateStakingReward(
            totalStake_BN,
            ONE_YEAR_SECONDS,
            TOTAL_AVAX_SUPPLY_BN
        );
        
        // console.log("-----------------------");
        // console.log("annualStakingReward            ", new Big(annualStakingReward.toString()).div(totalStake).times(100).toFixed());

        return annualStakingReward;
    }

    calculateStakingReward(amount: BN, duration: number, currentSupply: BN): BN {
        // console.log("-----------------------")
        // console.log("amount                         ", amount.toString());
        // console.log("duration                       ", duration);
        // console.log("currentSupply                  ", currentSupply.toString());

        let networkID = avalanche.getNetworkID();

        //@ts-ignore
        let defValues = Defaults.network[networkID];

        if (!defValues) {
            console.error("Network default values not found.");
            return new BN(0);
        }
        defValues = defValues.P;
       
        // console.log("-----------------------")

        let maxConsumption: number = defValues.maxConsumption;
        // console.log("maxConsumption                 ", maxConsumption);
        let minConsumption: number = defValues.minConsumption;
        // console.log("minConsumption                 ", minConsumption);
        let diffConsumption: number = maxConsumption - minConsumption;
        // console.log("diffConsumption                ", diffConsumption);
        let maxSupply: BN = defValues.maxSupply;
        // console.log("maxSupply                      ", maxSupply.toString());
        let maxStakingDuration: BN = defValues.maxStakingDuration;
        // console.log("maxStakingDuration             ", maxStakingDuration.toString());
        let remainingSupply = maxSupply.sub(currentSupply);
        // console.log("remainingSupply                ", remainingSupply.toString());
        

        // console.log("-----------------------")

        // console.log("ONEAVAX                        ", ONEAVAX.toString());

        // console.log("-----------------------")

        let amtBig = Big(amount.div(ONEAVAX).toString());
        // console.log("amtBig                         ", amtBig.toFixed());
        let currentSupplyBig = Big(currentSupply.div(ONEAVAX).toString());
        // console.log("currentSupplyBig               ", currentSupplyBig.toFixed());
        let remainingSupplyBig = Big(remainingSupply.div(ONEAVAX).toString());
        // console.log("remainingSupplyBig             ", remainingSupplyBig.toFixed());
        let portionOfExistingSupplyBig = amtBig.div(currentSupplyBig);
        // console.log("portionOfExistingSupplyBig     ", portionOfExistingSupplyBig.toFixed());

        let portionOfStakingDuration: number = duration / maxStakingDuration.toNumber();
        // console.log("portionOfStakingDuration       ", portionOfStakingDuration);
        let mintingRate: number = minConsumption + diffConsumption * portionOfStakingDuration;
        // console.log("mintingRate                    ", mintingRate);
        
        // console.log("-----------------------")
        let rewardBig: Big = remainingSupplyBig.times(
            portionOfExistingSupplyBig
        );
        rewardBig = rewardBig.times(
            Big(mintingRate * portionOfStakingDuration)
        );
        // console.log("rewardBig                      ", rewardBig.toFixed());

        let rewardStr = rewardBig.times(Math.pow(10, 9)).toFixed(0);
        // console.log("rewardStr                      ", rewardStr);
        let rewardBN = new BN(rewardStr);
        // console.log("rewardBN                       ", rewardBN.toString());

        return rewardBN;
    }
}
</script>
<style scoped lang="scss">
#network_statistics {
    color: $primary-color;
    /* background-color: $blue-light2; */
    /* color: $blue; */
}

.header {
    padding-bottom: 30px;
}

.meta_title {
    /* color: $blue; */
}

.one-column {
    grid-template-columns: 1fr;
    row-gap: 30px;
    margin-bottom: 25px;
    padding-bottom: 15px !important;
    border-bottom: 1px solid $primary-color-xlight;

    .meta {
        .meta_val {
            padding-top: 10px;
            font-size: 36px;

            .unit {
                font-size: 20px !important;
            }
        }
    }
}

.two-column {
    grid-template-columns: minmax(0, 0.75fr) minmax(0, 1fr);
    row-gap: 30px;
    column-gap: 30px;

    .meta {
        .meta_val {
            font-size: 20px;
        }
    }
}

.stats {
    display: grid;
    padding: 4px 0 0;
    flex-wrap: wrap;
    overflow: auto;

    /* hyperlink */
    .meta {
        font-size: 12px;
        display: flex;
        flex-grow: 1;
        justify-content: flex-start;
        flex-wrap: wrap;
        font-weight: 700;

        .stat {
            display: flex;
            flex-direction: column;

            &:hover {
                text-decoration: none !important;
                opacity: 0.7;
            }
        }

        .stat > div {
            display: flex;
        }

        p {
            padding: 2px 0;
            font-weight: 400;
        }

        .label {
            text-transform: capitalize;
            /* color: $blue; */
            font-size: 12px;
            font-weight: 700;
            margin-bottom: 6px;
            padding-left: 3px;
        }

        .meta_val {
            font-weight: 300;
            /* color: $blue; */
            line-height: 1em;

            .unit {
                font-family: "Rubik", sans-serif;
                font-size: 12px;
                opacity: 0.7;
            }
        }
    }
}

@include mdOnly {
    .stats {
        img {
            width: 24px;
        }
    }
}

@include smOnly {
    .stats {
        grid-template-columns: 50% 50%;
        grid-template-rows: max-content;

        > div {
            padding: 30px 0 0;
        }
    }
}

@include xsOnly {
    .stats {
        grid-template-columns: none;

        .one-column {
            grid-template-columns: 1fr;
            row-gap: 45px;
            margin-bottom: 45px;

            .meta {
                .meta_val {
                    font-size: 20px;

                    .unit {
                        font-size: 14px !important;
                    }
                }
            }
        }

        .two-column {
            .meta {
                .meta_val {
                    font-size: 20px;
                }
            }
        }

        .stat {
            .label {
                font-size: 13px;
            }

            .meta_val {
                font-size: 20px;

                .unit {
                    font-size: 14px !important;
                }
            }
        }
    }
}
</style>

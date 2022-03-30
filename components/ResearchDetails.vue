<template>
    <div>
        <div v-if="$parent.isResearchUnlocked(research.id) == false" class="row g-3">
            <div class="col-12 text-center">
                <div class="h5 text-gray">{{ $t('researchName_' + research.id) }}</div>
                <div class="small text-gray"><i class="fas fa-fw fa-lock"></i> Locked</div>
            </div>
            <div class="col-12 mt-4">
                <div class="row align-items-center gx-2 mb-1">
                    <div class="col-auto text-gray">
                        Requirements
                    </div>
                </div>
                <div v-for="(req, key) of research.reqs" :key="key">
                    <div class="row gx-2 align-items-baseline">
                        <span class="col text-normal">{{ $t('researchName_' + key) }}</span>
                        <span class="col-auto small" :class="{ 'text-success':$parent.researches[key].level >= req, 'text-normal':$parent.researches[key].level < req }">Lvl</span>
                        <span class="col-auto" :class="{ 'text-success':$parent.researches[key].level >= req, 'text-white':$parent.researches[key].level < req }">{{ req }}</span>
                    </div>
                </div>
            </div>
        </div>
        <div v-if="$parent.isResearchUnlocked(research.id) == true" class="row g-3">
            <div class="col-12 text-center">
                <div class="h5 text-primary">{{ $t('researchName_' + research.id) }}</div>
            </div>
            <div v-if="research.desc == true" class="col-12 mt-4 text-center">
                <span class="text-warning">{{ $t('researchDesc_' + research.id) }}</span>
            </div>
            <div class="col-12 mt-4">
                <div class="row align-items-baseline justify-content-center">
                    <div class="col-auto">
                        <span class="text-normal">Lvl</span>
                        <span class="ms-1 text-white">{{ research.level }}</span>
                    </div>
                    <div class="col-auto">
                        <i class="text-normal fas fa-fw fa-arrow-right"></i>
                    </div>
                    <div class="col-auto">
                        <span class="text-normal">Lvl</span>
                        <span class="ms-1 text-white">{{ research.level + 1 }}</span>
                    </div>
                </div>
            </div>
            <div v-for="(bonus, key) of buildingBonuses" :key="key" class="col-12 mt-4">
                <div class="text-gray">{{ $t('buildingName_' + key) }}</div>
                <div v-for="(prod, key) of bonus.prods" :key="key" class="row gx-2">
                    <span class="col text-normal">{{ $t('resName_' + key) }} production</span>
                    <span class="col-auto text-success">+{{ prod }} <small class="opacity-75">%</small></span>
                </div>
                <div v-if="bonus.researchPoint" class="row gx-2">
                    <span class="col text-normal">{{ $t('resName_researchPoint') }} production</span>
                    <span class="col-auto text-success">+{{ bonus.researchPoint }} <small class="opacity-75">%</small></span>
                </div>
            </div>
            <div v-for="(bonus, key) of research.getShipBonuses()" :key="key" class="col-12 mt-4">
                <div class="text-gray">{{ $t('shipName_' + key) }}</div>
                <div v-for="(stat, key) of bonus.stats" :key="key" class="row gx-2">
                    <span class="col text-normal">{{ $t('shipStat_' + key) }}</span>
                    <span class="col-auto text-success">+{{ stat }} <small class="opacity-75">%</small></span>
                </div>
            </div>
            <div v-if="research.getNextUnlock()" class="col-12 mt-4">
                <div class="text-gray">Unlocks</div>
                <div v-for="bId in research.getNextUnlock().buildings" :key="bId" class="text-normal">{{ $t('buildingName_' + bId) }}</div>
            </div>
            <div class="col-12 mt-4">
                <div class="row gx-2 align-items-baseline">
                    <div class="col-auto">
                        <span class="text-normal">Research Points</span>
                    </div>
                    <div class="col">
                        <span :class="{ 'text-danger':rpCost > $parent.researchPoint.count, 'text-success':rpCost <= $parent.researchPoint.count }"><FormatNumber :value="rpCost" /></span>
                    </div>
                    <div class="col-auto">
                        <button type="button" class="btn btn-sm btn-outline-primary" :class="{ 'disabled':$parent.canLevelUp(research.id) == false }" style="width:85px;" @click="$parent.levelUpResearch(research.id)"><i class="fas fa-fw fa-arrow-circle-up"></i> Level up</button>
                    </div>
                </div>
            </div>
            <div v-for="unlock in research.getFutureUnlocks()" :key="unlock.level" class="col-12 mt-4">
                <div class="text-gray">At level {{ unlock.level }}, unlocks</div>
                <div v-for="bId in unlock.buildings" :key="bId" class="text-normal">{{ $t('buildingName_' + bId) }}</div>
            </div>
        </div>
    </div>
</template>

<script>
export default {

    props: [ 'research' ],
    
    computed: {
    
        rpCost() { return this.research.getCost() },
        
        buildingBonuses() {
        
            let ret = {}
            
            for (let bId in this.research.getBuildingBonuses())
                if (this.$parent.isBuildingUnlocked(bId) == true)
                    ret[bId] = this.research.getBuildingBonuses()[bId]
            
            return ret
        }
    },
}
</script>
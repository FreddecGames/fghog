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
                <div class="small text-gray"><span class="text-normal">Lvl</span> <span class="ms-1 text-white">{{ research.level }}</span> <span v-if="research.max" class="">/{{ research.max }}</span></div>
            </div>
            <div v-if="research.desc == true" class="col-12 mt-4 text-center">
                <span class="text-warning">{{ $t('researchDesc_' + research.id) }}</span>
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
            <div v-if="research.unlocks" class="col-12 mt-4">
                <div class="text-gray">Unlocks</div>
                <div v-for="(value, key) of research.unlocks" :key="key" class="row gx-2">
                    <div class="col text-normal">{{ $t('buildingName_' + key) }}</div>
                    <div class="col-auto"><small class="text-normal">at level</small> <span class="text-white">{{ value }}</span></div>
                </div>
            </div>
            <div v-if="research.buildingBonuses" class="col-12 mt-4">
                <div class="row g-2">
                    <div v-for="(bonuses, bId) of research.buildingBonuses" :key="bId" class="col-12">
                        <div class="text-gray">{{ $t('buildingName_' + bId) }}</div>
                        <div v-for="bonus in bonuses" class="row gx-2">
                            <div class="col text-normal"><span v-if="bonus.attrib != 'researchPoint'">{{ $t('resName_' + bonus.rId) }}</span> {{ $t('attrib_' + bonus.attrib) }}</div>
                            <div class="col-auto text-success"><span v-if="bonus.value > 0">+</span>{{ bonus.value }} <small class="opacity-75">%</small> <small>at level {{ bonus.minLevel }}</small></div>
                        </div>
                    </div>
                </div>
            </div>
            <div v-if="research.shipBonuses" class="col-12 mt-4">
                <div v-for="(bonuses, sId) of research.shipBonuses" :key="sId">
                    <div class="text-gray">{{ $t('shipName_' + sId) }}</div>
                    <div v-for="bonus in bonuses" class="row gx-2">
                        <div class="col text-normal">{{ $t('attrib_' +  bonus.attrib) }}</div>
                        <div class="col-auto text-success">+{{ bonus.value }} <small class="opacity-75">%</small> <small>at level {{ bonus.minLevel }}</small></div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {

    props: [ 'research' ],
    
    computed: {
    
        rpCost() { return this.research.getCost() },
    },
}
</script>
<template>
    <div>
        <div v-if="tech.unlocked == false" class="row g-3">
            <div class="col-12 text-center">
                <div class="h5 text-gray">{{ $t('techName_' + tech.def.id) }}</div>
                <div class="small text-gray"><i class="fas fa-fw fa-lock"></i> Locked</div>
            </div>
            <div class="col-12 mt-4">
                <div class="row align-items-center gx-2 mb-1">
                    <div class="col-auto text-gray">
                        Requirements
                    </div>
                </div>
                <div v-for="(req, key) of tech.reqTechs" :key="key">
                    <div class="row gx-2 align-items-baseline">
                        <span class="col text-normal">{{ $t('techName_' + key) }}</span>
                        <span class="col-auto small" :class="{ 'text-success':$parent.techs[key].level >= req, 'text-normal':$parent.techs[key].level < req }">Lvl</span>
                        <span class="col-auto" :class="{ 'text-success':$parent.techs[key].level >= req, 'text-white':$parent.techs[key].level < req }">{{ req }}</span>
                    </div>
                </div>
            </div>
        </div>
        <div class="row g-3">
            <div class="col-12 text-center">
                <div class="h5 text-primary">{{ $t('techName_' + tech.def.id) }}</div>
                <div class="small text-gray"><span class="text-normal">Lvl</span> <span class="ms-1 text-white">{{ tech.level }}</span> <span v-if="tech.def.max" class="">/{{ tech.def.max }}</span></div>
            </div>
            <div v-if="tech.desc == true" class="col-12 mt-4 text-center">
                <span class="text-warning">{{ $t('techDesc_' + tech.def.id) }}</span>
            </div>
            <div class="col-12 mt-4">
                <div class="row gx-2 align-items-baseline">
                    <div class="col-auto">
                        <span class="text-normal">Research Points</span>
                    </div>
                    <div class="col">
                        <span :class="{ 'text-danger':tech.canLevelUpRP() == false, 'text-success':tech.canLevelUpRP() == true }"><FormatNumber :value="tech.getCostRP()" /></span>
                    </div>
                    <div class="col-auto">
                        <button type="button" class="btn btn-sm btn-outline-primary" :class="{ 'disabled':tech.canLevelUpRP() == false }" style="width:85px;" @click="tech.levelUpRP()"><i class="fas fa-fw fa-arrow-circle-up"></i> Level up</button>
                    </div>
                </div>
            </div>
            <div v-if="tech.def.buildingUnlocks || tech.def.shipUnlocks" class="col-12 mt-4">
                <div class="text-gray">Unlocks</div>
                <div v-for="(value, key) of tech.def.buildingUnlocks" :key="key" class="row gx-2">
                    <div class="col text-normal">{{ $t('buildingName_' + key) }}</div>
                    <div v-if="tech.level < value" class="col-auto"><small class="text-normal">at level</small> <span class="text-white">{{ value }}</span></div>
                    <div v-if="tech.level >= value" class="col-auto"><small class="text-success"><i class="fas fa-fw fa-check-circle"></i></small></div>
                </div>
                <div v-for="(value, key) of tech.def.shipUnlocks" :key="key" class="row gx-2">
                    <div class="col text-normal">{{ $t('shipName_' + key) }}</div>
                    <div v-if="tech.level < value" class="col-auto"><small class="text-normal">at level</small> <span class="text-white">{{ value }}</span></div>
                    <div v-if="tech.level >= value" class="col-auto"><small class="text-success"><i class="fas fa-fw fa-check-circle"></i></small></div>
                </div>
            </div>
            <div v-if="tech.def.buildingBonuses" class="col-12 mt-4">
                <div class="row g-2">
                    <div v-for="(bonuses, bId) of tech.def.buildingBonuses" :key="bId" class="col-12">
                        <div class="text-gray">{{ $t('buildingName_' + bId) }}</div>
                        <div v-for="bonus in bonuses" class="row gx-2">
                            <div class="col text-normal"><span v-if="bonus.attrib != 'researchPoint' && bonus.attrib != 'energy'">{{ $t('resName_' + bonus.rId) }}</span> {{ $t('attrib_' + bonus.attrib) }}</div>
                            <div v-if="tech.level < bonus.minLevel" class="col-auto text-white"><span v-if="bonus.value > 0">+</span>{{ bonus.value }} <small class="opacity-75">%</small> <small class="text-normal">at level {{ bonus.minLevel }}</small></div>
                            <div v-if="tech.level >= bonus.minLevel" class="col-auto text-success"><span v-if="bonus.value > 0">+</span>{{ bonus.value }} <small class="opacity-75">%</small></div>
                        </div>
                    </div>
                </div>
            </div>
            <div v-if="tech.def.shipBonuses" class="col-12 mt-4">
                <div class="row g-2">
                    <div v-for="(bonuses, sId) of tech.def.shipBonuses" :key="sId" class="col-12">
                        <div class="text-gray">{{ $t('shipName_' + sId) }}</div>
                        <div v-for="bonus in bonuses" class="row gx-2">
                            <div class="col text-normal">{{ $t('attrib_' +  bonus.attrib) }}</div>
                            <div v-if="tech.level < bonus.minLevel" class="col-auto text-white"><span v-if="bonus.value > 0">+</span>{{ bonus.value }} <small class="opacity-75">%</small> <small class="text-normal">at level {{ bonus.minLevel }}</small></div>
                            <div v-if="tech.level >= bonus.minLevel" class="col-auto text-success"><span v-if="bonus.value > 0">+</span>{{ bonus.value }} <small class="opacity-75">%</small></div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {

    props: [ 'tech' ],
}
</script>
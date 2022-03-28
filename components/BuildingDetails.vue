<template>
    <div class="row g-3">
        <div class="col-12 text-center">
            <div class="h5 text-primary">{{ $t('buildingName_' + building.id) }}</div>
        </div>
        <div v-if="building.count <= 0" class="col-12 mt-4">
            <div class="row align-items-center gx-2 mb-1">
                <div class="col-auto text-gray">
                    <i class="text-success medium fas fa-fw fa-power-off me-2 opacity-75"></i>
                    Unitary Production
                </div>
            </div>
            <div v-for="(res, key) of $parent.currentPlanet.getRawProduction(building.id, 1)" :key="key">
                <div class="row gx-2">
                    <span class="col text-normal">{{ $t('resName_' + key) }}</span>
                    <span class="col-auto" :class="{ 'text-success':res > 0, 'text-danger':res < 0 }"><span v-if="res > 0">+</span><FormatNumber :value="res" /> <small class="opacity-75">/s</small></span>
                </div>
            </div>
        </div>
        <div v-if="building.count > 0" class="col-12 mt-4">
            <div class="row align-items-center gx-2 mb-1">
                <div class="col-auto text-gray">
                    <i v-if="building.active == true" class="text-success medium fas fa-fw fa-power-off me-2"></i>
                    <i v-if="building.active == false" class="text-danger medium fas fa-fw fa-power-off me-2"></i>
                    Production
                </div>
                <div class="col-auto text-white medium"><span class="px-1">{{ building.count.toLocaleString() }}</span></div>
            </div>
            <div v-if="building.hasNeeds() == true" class="text-danger medium my-1">
                <i class="fas fa-fw fa-exclamation-triangle me-2"></i> Stopped due to lack of <span v-for="(res, key) of building.needs" :key="key"><span v-if="building.needs[key] == true">{{ $t('resName_' + key) }}</span></span>
            </div>
            <div v-for="(res, key) of $parent.currentPlanet.getRawProduction(building.id, building.count)" :key="key">
                <div class="row gx-2">
                    <span class="col text-normal">{{ $t('resName_' + key) }}</span>
                    <span class="col-auto" :class="{ 'text-success':res > 0, 'text-danger':res < 0 }"><span v-if="res > 0">+</span><FormatNumber :value="res" /> <small class="opacity-75">/s</small></span>
                </div>
            </div>
            <div class="mt-1 text-end">
                <button v-if="building.active == true" type="button" class="btn btn-sm btn-outline-primary" style="width:75px;" @click="building.toggleActive()">
                    <i class="fas fa-fw fa-power-off"></i>
                    <span>Disable</span>
                </button>
                <button v-if="building.active == false" type="button" class="btn btn-sm btn-outline-primary" style="width:75px;" @click="building.toggleActive()">
                    <i class="fas fa-fw fa-power-off"></i>
                    <span>Enable</span>
                </button>
            </div>
        </div>
        <div class="col-12 mt-4">
            <div class="row align-items-center gx-2 mb-1">
                <div class="col-auto text-gray">Building Costs</div>
                <div class="col-auto btn-group btn-group-sm" role="group">
                    <button type="button" class="btn btn-sm py-0" :class="{ 'text-white':$parent.currentBuildCount == 1 }" @click="$parent.currentBuildCount = 1">1</button>
                    <button type="button" class="btn btn-sm py-0" :class="{ 'text-white':$parent.currentBuildCount == 10 }" @click="$parent.currentBuildCount = 10">10</button>
                    <button type="button" class="btn btn-sm py-0" :class="{ 'text-white':$parent.currentBuildCount == 50 }" @click="$parent.currentBuildCount = 50">50</button>
                </div>
            </div>
            <div v-for="(res, key) of $parent.currentPlanet.getBuildingCost(building.id, $parent.currentBuildCount)" :key="key">
                <div v-if="res >= 1" class="row gx-2">
                    <span class="col text-normal">{{ $t('resName_' + key) }}</span>
                    <span class="col-auto" :class="{ 'text-white':res <= $parent.currentPlanet.resources[key].count, 'text-danger':res > $parent.currentPlanet.resources[key].count }"><FormatNumber :value="res" /></span>
                </div>
            </div>
            <div v-if="$parent.currentPlanet.canBuy($parent.currentPlanet.getBuildingCost(building.id, $parent.currentBuildCount))" class="text-end mt-1">
                <button type="button" class="btn btn-sm btn-outline-primary" style="width:75px;" @click="$parent.currentPlanet.queueBuilding(building.id, $parent.currentBuildCount)"><i class="fas fa-fw fa-plus-circle"></i> Build</button>
            </div>
            <div v-if="!$parent.currentPlanet.canBuy($parent.currentPlanet.getBuildingCost(building.id, $parent.currentBuildCount))" class="text-end mt-1">
                <button type="button" class="btn btn-sm btn-outline-primary" style="width:75px;" @click="$parent.currentPlanet.queueBuilding(building.id, $parent.currentBuildCount)"><i class="fas fa-fw fa-plus-circle"></i> Queue</button>
            </div>
        </div>
        <div v-if="building.count > 0" class="col-12 mt-4">
            <div class="row align-items-center gx-2 mb-1">
                <div class="col-auto text-gray">Destruction Refunds</div>
                <div class="col-auto btn-group btn-group-sm" role="group">
                    <button type="button" class="btn btn-sm py-0" :class="{ 'text-white':$parent.currentDestroyCount == 1 }" @click="$parent.currentDestroyCount = 1">1</button>
                    <button type="button" class="btn btn-sm py-0" :class="{ 'text-white':$parent.currentDestroyCount == 10 }" @click="$parent.currentDestroyCount = 10">10</button>
                    <button type="button" class="btn btn-sm py-0" :class="{ 'text-white':$parent.currentDestroyCount == 50 }" @click="$parent.currentDestroyCount = 50">50</button>
                </div>
            </div>
            <div v-if="building.count >= $parent.currentDestroyCount" v-for="(res, key) of $parent.currentPlanet.getBuildingRefund(building.id, $parent.currentDestroyCount)" :key="key">
                <div v-if="res >= 1" class="row gx-2">
                    <span class="col text-normal">{{ $t('resName_' + key) }}</span>
                    <span class="col-auto text-white"><FormatNumber :value="res" /></span>
                </div>
            </div>
            <div class="text-end mt-1">
                <button type="button" class="btn btn-sm btn-outline-primary" style="width:75px;" @click="$parent.currentPlanet.destroyBuilding(building.id, $parent.currentDestroyCount)"><i class="fas fa-fw fa-minus-circle"></i> Destroy</button>
            </div>
            <div v-if="building.count < $parent.currentDestroyCount" class="mt-1 text-end">
                <span class="medium text-danger">You don't have {{ $parent.currentDestroyCount }} {{ $t('buildingName_' + building.id) }}.</span>
            </div>
        </div>
    </div>
</template>

<script>
export default {

    props: [ 'building' ],    
}
</script>
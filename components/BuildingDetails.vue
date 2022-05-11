<template>
    <div class="row g-3">
        <div class="col-12 text-center">
            <div class="h5 text-primary">{{ $t('buildingName_' + building.ref.id) }}</div>
        </div>
        <div v-if="building.ref.desc == true" class="col-12 text-center">
            <span class="text-warning">{{ $t('buildingDesc_' + building.ref.id) }}</span>
        </div>
        <div v-if="building.count <= 0 && (building.ref.prod || building.ref.energy || building.ref.researchPoint)" class="col-12 mt-4">
            <div class="row align-items-center gx-2 mb-2">
                <div class="col-auto text-gray">
                    <span class="h6">Production</span>
                </div>
            </div>
            <div v-for="(res, key) of building.getProd(1)" :key="key">
                <div class="row gx-2">
                    <span class="col text-normal">{{ $t('resName_' + key) }}</span>
                    <span class="col-auto" :class="{ 'text-success':res > 0, 'text-danger':res < 0 }"><span v-if="res > 0">+</span><FormatNumber :value="res" /> <small class="opacity-75">/s</small></span>
                </div>
            </div>
        </div>
        <div v-if="building.count > 0 && (building.ref.prod || building.ref.energy || building.ref.researchPoint)" class="col-12 mt-4">
            <div class="row align-items-center gx-2 mb-2">
                <div class="col-auto text-gray">
                    <span class="h6">Production</span>
                </div>
            </div>
            <div v-if="building.hasNeed() == true" class="text-danger medium my-1">
                <div class="row gx-2 mb-2">
                    <div class="col-auto">
                        <i class="fas fa-fw fa-exclamation-triangle"></i>
                    </div>
                    <div class="col-auto">
                        Stopped due to lack of
                        <span v-for="(res, key) of building.need" :key="key" class="col-auto" v-if="building.need[key] == true">{{ $t('resName_' + key) }}</span>
                    </div>
                </div>
            </div>
            <div v-for="(res, key) of building.getProd(1)" :key="key">
                <div class="row gx-2">
                    <span class="col text-normal">{{ $t('resName_' + key) }}</span>
                    <span class="col-auto" :class="{ 'text-success':res > 0, 'text-danger':res < 0 }"><span v-if="res > 0">+</span><FormatNumber :value="res" /> <small class="opacity-75">/s</small></span>
                </div>
            </div>
            <div class="mt-2 text-end">
                <button v-if="building.active == true" type="button" class="w-100 btn border" @click="building.toggleActive()">
                    <span>Disable</span>
                </button>
                <button v-if="building.active == false" type="button" class="w-100 btn border" @click="building.toggleActive()">
                    <span>Enable</span>
                </button>
            </div>
        </div>
        <div class="col-12 mt-4">
            <div class="row align-items-center gx-2 mb-2">
                <div class="col text-gray">
                    <span class="h6">Building</span>
                </div>
                <div class="col-auto btn-group" role="group">
                    <button type="button" class="btn border py-1" :class="{ 'text-white':$parent.currentBuildCount == 1 }" @click="$parent.currentBuildCount = 1">1</button>
                    <button type="button" class="btn border py-1" :class="{ 'text-white':$parent.currentBuildCount == 10 }" @click="$parent.currentBuildCount = 10">10</button>
                    <button type="button" class="btn border py-1" :class="{ 'text-white':$parent.currentBuildCount == 50 }" @click="$parent.currentBuildCount = 50">50</button>
                </div>
            </div>
            <div class="mb-2">
                <div v-for="(res, key) of building.getCost($parent.currentBuildCount)" :key="key">
                    <div v-if="res >= 1" class="row gx-2">
                        <div class="col text-normal">{{ $t('resName_' + key) }}</div>
                        <div class="col-auto"><span :class="{ 'text-white':res <= $parent.currentPlanet.resources[key].count, 'text-danger':res > $parent.currentPlanet.resources[key].count }"><FormatNumber :value="res" /></span></div>
                    </div>
                </div>
            </div>
            <div v-if="building.canBuild($parent.currentBuildCount)" class="mt-2">
                <button type="button" class="w-100 btn btn border" @click="building.addQueue($parent.currentBuildCount)">Build</button>
            </div>
            <div v-if="!building.canBuild($parent.currentBuildCount)" class="mt-2">
                <button type="button" class="w-100 btn btn border" @click="building.addQueue($parent.currentBuildCount)">Queue</button>
            </div>
        </div>
        <div v-if="building.count > 0" class="col-12 mt-4">
            <div class="row align-items-center gx-2 mb-2">
                <div class="col text-gray">
                    <span class="h6">Destruction</span>
                </div>
                <div class="col-auto btn-group" role="group">
                    <button type="button" class="btn border py-1" :class="{ 'text-white':$parent.currentDestroyCount == 1 }" @click="$parent.currentDestroyCount = 1">1</button>
                    <button type="button" class="btn border py-1" :class="{ 'text-white':$parent.currentDestroyCount == 10 }" @click="$parent.currentDestroyCount = 10">10</button>
                    <button type="button" class="btn border py-1" :class="{ 'text-white':$parent.currentDestroyCount == 50 }" @click="$parent.currentDestroyCount = 50">50</button>
                </div>
            </div>
            <div class="mb-2">
                <div v-if="building.count >= $parent.currentDestroyCount" v-for="(res, key) of building.getRefund($parent.currentDestroyCount)" :key="key">
                    <div v-if="res >= 1" class="row gx-2">
                        <span class="col text-normal">{{ $t('resName_' + key) }}</span>
                        <span class="col-auto text-white"><FormatNumber :value="res" /></span>
                    </div>
                </div>
            </div>
            <div v-if="building.count >= $parent.currentDestroyCount" class="text-end mt-2">
                <button type="button" class="w-100 btn btn border" style="width:75px;" @click="building.destroy($parent.currentDestroyCount)">Destroy</button>
            </div>
            <div v-if="building.count < $parent.currentDestroyCount" class="mt-2 text-end">
                <span class="medium text-danger">You don't have {{ $parent.currentDestroyCount }} {{ $t('buildingName_' + building.ref.id) }}.</span>
            </div>
        </div>
    </div>
</template>

<script>
export default {

    props: [ 'building' ],    
}
</script>
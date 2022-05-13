<template>
    <div class="row g-3">
        <div class="col-12">
            <div class="row gx-2 align-items-center justify-content-center">
                <div class="col-auto">
                    <div class="h6 text-primary">{{ travel.fleet.name }}</div>
                </div>
                <div class="col-auto">
                    <button type="button" class="w-100 btn btn-sm border" @click="$parent.popupRenameName = travel.fleet.name; $parent.popupRenameFleet = travel.fleet;">
                        <i class="fas fa-fw fa-pen"></i>
                    </button>
                </div>
            </div>
            <div v-if="travel.type == 'autoroute'" class="text-center">
                <span class="medium text-gray">Autoroute</span>
            </div>
        </div>
        <div class="col-12">
            <div class="row gx-2">
                <div class="col"><span class="text-normal">Experience</span></div>
                <div class="col-auto"><span class="text-white">{{ travel.fleet.experience }}</span></div>
            </div>
            <div class="row gx-2">
                <div class="col"><span class="text-normal">Military Value</span></div>
                <div class="col-auto"><span class="text-white"><FormatNumber :value="travel.fleet.getMilitaryValue()" /></span></div>
            </div>
            <div class="row gx-2">
                <div class="col"><span class="text-normal">Total Power</span></div>
                <div class="col-auto"><span class="text-white"><FormatNumber :value="travel.fleet.getPower()" /></span></div>
            </div>
            <div class="row gx-2">
                <div class="col"><span class="text-normal">Total HP</span></div>
                <div class="col-auto"><span class="text-white"><FormatNumber :value="travel.fleet.getHP()" /></span></div>
            </div>
            <div class="row gx-2">
                <div class="col"><span class="text-normal">Speed</span></div>
                <div class="col-auto"><span class="text-white"><FormatNumber :value="travel.fleet.getSpeed()" /></span></div>
            </div>
            <div class="row gx-2 mb-2">
                <div class="col"><span class="text-normal">Storage</span></div>
                <div class="col-auto">
                    <span class="text-white"><FormatNumber :value="travel.fleet.getCurrentStorage()" /></span>
                    <small class="text-gray">/<FormatNumber :value="travel.fleet.getTotalStorage()" /></small>
                </div>
            </div>
            <div v-for="(count, sId) in travel.fleet.getShips()" :key="sId" class="row gx-2">
                <div class="col"><span class="text-normal">{{ $t('shipName_' + sId) }}</span></div>
                <div class="col-auto"><span class="text-white"><FormatNumber :value="count" /></span></div>
            </div>
            <div v-if="travel.fleet.getAvailableResources().length > 0" class="mt-2">
                <div v-for="res in travel.fleet.getAvailableResources()" :key="res.id" class="row gx-2">
                    <div class="col"><span class="text-normal">{{ $t('resName_' + res.id) }}</span></div>
                    <div class="col-auto"><span class="text-white"><FormatNumber :value="res.count" /></span></div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {

    props: [ 'travel' ],    
}
</script>
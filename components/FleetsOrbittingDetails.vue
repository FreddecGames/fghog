<template>
    <div class="row g-3">
        <div class="col-12">
            <div class="row gx-2 align-items-center justify-content-center">
                <div class="col-auto">
                    <div class="h6 text-primary">{{ fleet.name }}</div>
                </div>
                <div class="col-auto">
                    <button type="button" class="w-100 btn btn-sm border" @click="$parent.popupRenameName = fleet.name; $parent.popupRenameFleet = fleet;">
                        <i class="fas fa-fw fa-pen"></i>
                    </button>
                </div>
            </div>
        </div>
        <div class="col-12">
            <div class="row gx-2">
                <div class="col"><span class="text-normal">Experience</span></div>
                <div class="col-auto"><span class="text-white">{{ fleet.experience }}</span></div>
            </div>
            <div class="row gx-2">
                <div class="col"><span class="text-normal">Military Value</span></div>
                <div class="col-auto"><span class="text-white"><FormatNumber :value="fleet.getMilitaryValue()" /></span></div>
            </div>
            <div class="row gx-2">
                <div class="col"><span class="text-normal">Total Power</span></div>
                <div class="col-auto"><span class="text-white"><FormatNumber :value="fleet.getPower()" /></span></div>
            </div>
            <div class="row gx-2">
                <div class="col"><span class="text-normal">Total HP</span></div>
                <div class="col-auto"><span class="text-white"><FormatNumber :value="fleet.getHP()" /></span></div>
            </div>
            <div class="row gx-2">
                <div class="col"><span class="text-normal">Speed</span></div>
                <div class="col-auto"><span class="text-white"><FormatNumber :value="fleet.getSpeed()" /></span></div>
            </div>
            <div class="row gx-2 mb-2">
                <div class="col"><span class="text-normal">Storage</span></div>
                <div class="col-auto">
                    <span class="text-white"><FormatNumber :value="fleet.getCurrentStorage()" /></span>
                    <small class="text-gray">/<FormatNumber :value="fleet.getTotalStorage()" /></small>
                </div>
            </div>
            <div v-for="(count, sId) in fleet.getShips()" :key="sId" class="row gx-2">
                <div class="col"><span class="text-normal">{{ $t('shipName_' + sId) }}</span></div>
                <div class="col-auto"><span class="text-white"><FormatNumber :value="count" /></span></div>
            </div>
            <div v-if="fleet.getAvailableResources().length > 0" class="mt-2">
                <div v-for="res in fleet.getAvailableResources()" :key="res.id" class="row gx-2">
                    <div class="col"><span class="text-normal">{{ $t('resName_' + res.id) }}</span></div>
                    <div class="col-auto"><span class="text-white"><FormatNumber :value="res.count" /></span></div>
                </div>
            </div>
        </div>
        <div v-if="fleet.planet.ref.civId == 'human' && fleet.getAvailableStorage() > 0" class="col-12">
            <button type="button" class="w-100 btn border" @click="$parent.showPopupLoad(fleet)">
                Load
            </button>
        </div>
        <div v-if="fleet.planet.ref.civId == 'human' && fleet.getCurrentStorage() > 0" class="col-12">
            <button type="button" class="w-100 btn border" @click="$parent.showPopupUnload(fleet)">
                Unload
            </button>
        </div>
        <div class="col-12">
            <button type="button" class="w-100 btn border" @click="$parent.popupMoveFleet = fleet">
                Move to
            </button>
        </div>
        <div class="col-12">
            <button type="button" class="w-100 btn border" @click="$parent.showPopupAutoroute(fleet)">
                Autoroute
            </button>
        </div>
        <div v-if="fleet.planet.ref.civId != 'human' && fleet.planet.getEnemyFleets().length < 1" class="col-12">
            <button type="button" class="w-100 btn border" @click="fleet.colonize()">
                Colonize
            </button>
        </div>
        <div v-if="fleet.planet.getEnemyFleets().length > 0" class="col-12">
            <button type="button" class="w-100 btn border" @click="$parent.showAttackPopup(fleet)">
                Attack
            </button>
        </div>
    </div>
</template>

<script>
export default {

    props: [ 'fleet' ],    
}
</script>
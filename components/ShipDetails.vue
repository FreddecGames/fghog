<template>
    <div class="row g-3">
        <div class="col-12 text-center">
            <div class="h5 text-primary">{{ $t('shipName_' + ship.id) }}</div>
        </div>
        <div class="col-12 mt-4">
            <div class="row align-items-center gx-2 mb-1">
                <div class="col-auto text-gray">Building Costs</div>
                <div class="col-auto btn-group btn-group-sm" role="group">
                    <button type="button" class="btn btn-sm py-0" :class="{ 'text-white':$parent.currentShipCount == 1 }" @click="$parent.currentShipCount = 1">1</button>
                    <button type="button" class="btn btn-sm py-0" :class="{ 'text-white':$parent.currentShipCount == 10 }" @click="$parent.currentShipCount = 10">10</button>
                    <button type="button" class="btn btn-sm py-0" :class="{ 'text-white':$parent.currentShipCount == 50 }" @click="$parent.currentShipCount = 50">50</button>
                </div>
            </div>
            <div v-for="(res, key) of $parent.currentPlanet.getShipCost(ship.id, $parent.currentShipCount)" :key="key">
                <div v-if="res >= 1" class="row gx-2">
                    <span class="col text-normal">{{ $t('resName_' + key) }}</span>
                    <span class="col-auto" :class="{ 'text-white':res <= $parent.currentPlanet.resources[key].count, 'text-danger':res > $parent.currentPlanet.resources[key].count }"><FormatNumber :value="res" /></span>
                </div>
            </div>
            <div v-if="$parent.currentPlanet.canBuy($parent.currentPlanet.getShipCost(ship.id, $parent.currentShipCount)) == true" class="text-end mt-2">
                <button type="button" class="btn btn-sm btn-outline-primary" style="width:75px;" @click="$parent.currentPlanet.buildShip(ship.id, $parent.currentShipCount)"><i class="fas fa-fw fa-plus-circle"></i> Build</button>
            </div>
            <div v-if="$parent.currentPlanet.canBuy($parent.currentPlanet.getShipCost(ship.id, $parent.currentShipCount)) == false" class="text-end mt-2">
                <button type="button" class="btn btn-sm btn-outline-primary disabled" style="width:75px;"><i class="fas fa-fw fa-plus-circle"></i> Build</button>
            </div>
        </div>
    </div>
</template>

<script>
export default {

    props: [ 'ship' ],    
}
</script>
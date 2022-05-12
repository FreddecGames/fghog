<template>
    <div class="row g-3">
        <div class="col-12 text-center">
            <div class="h5 text-primary">{{ $t('shipName_' + ship.ref.id) }}</div>
            <div class="text-center"><span class="text-gray">{{ $t('shipType_' + ship.ref.type) }}</span></div>
        </div>
        <div class="col-12 mt-4">
            <div class="row gx-2">
                <div class="col"><span class="text-normal">Weapon</span></div>
                <div class="col-auto"><span class="text-white">{{ $t('weaponName_' + ship.ref.weapon ) }}</span></div>
            </div>
            <div class="row gx-2">
                <div class="col"><span class="text-normal">HP</span></div>
                <div class="col-auto"><span class="text-white"><FormatNumber :value="ship.ref.hp" /></span></div>
            </div>
            <div v-if="ship.ref.power > 0" class="row gx-2">
                <div class="col"><span class="text-normal">Power</span></div>
                <div class="col-auto"><span class="text-white"><FormatNumber :value="ship.ref.power" /></span></div>
            </div>
            <div v-if="ship.ref.piercing > 0" class="row gx-2">
                <div class="col"><span class="text-normal">Piercing</span></div>
                <div class="col-auto"><span class="text-white"><FormatNumber :value="Math.min(100, Math.floor(100 * ship.ref.piercing) / 100)" /> <small class="opacity-75">%</small></span></div>
            </div>
            <div v-if="ship.ref.shield > 0" class="row gx-2">
                <div class="col"><span class="text-normal">Shield</span></div>
                <div class="col-auto"><span class="text-white"><FormatNumber :value="ship.ref.shield" /></span></div>
            </div>
            <div v-if="ship.ref.armor >= 800" class="row gx-2">
                <div class="col"><span class="text-normal">Armor</span></div>
                <div class="col-auto"><span class="text-white"><FormatNumber :value="Math.floor(100 * (100 - 100 / (1 + Math.log(1 + ship.ref.armor / 1e4) / Math.log(2)))) / 100" /> <small class="opacity-75">%</small></span></div>
            </div>
            <div class="row gx-2">
                <div class="col"><span class="text-normal">Speed</span></div>
                <div class="col-auto"><span class="text-white">{{ ship.ref.speed }}</span></div>
            </div>
            <div v-if="ship.ref.storage > 0" class="row gx-2">
                <div class="col"><span class="text-normal">Storage</span></div>
                <div class="col-auto"><span class="text-white"><FormatNumber :value="ship.ref.storage" /></span></div>
            </div>
        </div>
        <div v-if="ship.isUnlocked() == false" class="col-12 mt-4 text-center">
            <span class="text-danger">Locked</span>
        </div>
        <div v-if="ship.isUnlocked() == true" class="col-12 mt-4">
            <div class="row align-items-center gx-2 mb-2">
                <div class="col text-gray">
                    <span class="h6">Building</span>
                </div>
                <div class="col-auto btn-group btn-group-sm" role="group">
                    <button type="button" class="btn border py-1" :class="{ 'text-white':$parent.currentShipCount == 1 }" @click="$parent.currentShipCount = 1">1</button>
                    <button type="button" class="btn border py-1" :class="{ 'text-white':$parent.currentShipCount == 10 }" @click="$parent.currentShipCount = 10">10</button>
                    <button type="button" class="btn border py-1" :class="{ 'text-white':$parent.currentShipCount == 100 }" @click="$parent.currentShipCount = 100">100</button>
                    <button type="button" class="btn border py-1" :class="{ 'text-white':$parent.currentShipCount == 1000 }" @click="$parent.currentShipCount = 1000">1<small class="opacity-75"> K</small></button>
                    <button type="button" class="btn border py-1" :class="{ 'text-white':$parent.currentShipCount == 10000 }" @click="$parent.currentShipCount = 10000">10<small class="opacity-75"> K</small></button>
                </div>
            </div>
            <div v-for="(res, key) of ship.getCost($parent.currentShipCount)" :key="key">
                <div v-if="res >= 1" class="row gx-2">
                    <span class="col text-normal">{{ $t('resName_' + key) }}</span>
                    <span class="col-auto" :class="{ 'text-white':res <= $parent.currentPlanet.resources[key].count, 'text-danger':res > $parent.currentPlanet.resources[key].count }"><FormatNumber :value="res" /></span>
                </div>
            </div>
            <div v-if="$parent.currentPlanet.canBuy(ship.getCost($parent.currentShipCount)) == true" class="text-end mt-2">
                <button type="button" class="w-100 btn btn border" @click="ship.build($parent.currentShipCount)">Build</button>
            </div>
            <div v-if="$parent.currentPlanet.canBuy(ship.getCost($parent.currentShipCount)) == false" class="text-end mt-2">
                <button type="button" class="w-100 btn btn border disabled">Build</button>
            </div>
        </div>
    </div>
</template>

<script>
export default {

    props: [ 'ship' ],    
}
</script>
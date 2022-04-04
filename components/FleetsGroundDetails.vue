<template>
    <div class="row g-3">
        <div class="col-12 text-center">
            <div class="h6 text-primary">{{ $t('planetName_' + planet.id) }}</div>
        </div>
        <div v-if="planet.getGroundShipCount() <= 0" class="col-12 text-center">
            <span class="text-gray">There is no ship on the ground</span>
        </div>
        <div v-if="planet.getGroundShipCount() > 0" class="col-12">
            <div v-for="(ship, key) of planet.ships" :key="key">
                <div v-if="ship.count > 0" class="row gx-2">
                    <span class="col text-normal">{{ $t('shipName_' + key) }}</span>
                    <span class="col-auto text-white">{{ ship.count.toLocaleString() }}</span>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {

    props: [ 'planet' ],    
}
</script>
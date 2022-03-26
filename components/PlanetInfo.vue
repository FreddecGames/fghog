<template>
    <div class="col-12">
        <div class="row gx-2">
            <span class="col text-normal">Type</span>
            <span class="col-auto text-white">{{ $t('planetType_' + planet.type) }}</span>
        </div>
        <div class="row gx-2">
            <span class="col text-normal">Radius</span>
            <span class="col-auto text-white">{{ planet.radius.toLocaleString() }} km</span>
        </div>
        <div class="row gx-2">
            <span class="col text-normal">Temperature</span>
            <span class="col-auto text-white">{{ planet.temp }} °C</span>
        </div>
        <div class="row gx-2">
            <span class="col text-normal">Atmosphere</span>
            <span class="col-auto text-white">{{ $t('planetAtmos_' + planet.atmos) }}</span>
        </div>
        <div class="row gx-2">
            <span class="col text-normal">Orbital Distance</span>
            <span class="col-auto text-white">{{ planet.orbit }} AU</span>
        </div>
        <div class="row gx-2">
            <span class="col text-normal">Influence</span>
            <span class="col-auto text-white">{{ planet.influence.toLocaleString() }}</span>
        </div>
    </div>
</template>

<script>
export default {
    props: [ 'planet' ],
}
</script>
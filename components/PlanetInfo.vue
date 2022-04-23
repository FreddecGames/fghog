<template>
    <div class="col-12">
        <div class="row gx-2">
            <span class="col text-normal">Type</span>
            <span class="col-auto text-white">{{ $t('planetType_' + planet.def.type) }}</span>
        </div>
        <div class="row gx-2">
            <span class="col text-normal">Radius</span>
            <span class="col-auto text-white">{{ planet.def.radius.toLocaleString() }} km</span>
        </div>
        <div class="row gx-2">
            <span class="col text-normal">Temperature</span>
            <span class="col-auto text-white">{{ planet.def.temp }} °C</span>
        </div>
        <div class="row gx-2">
            <span class="col text-normal">Atmosphere</span>
            <span class="col-auto text-white">{{ $t('planetAtmos_' + planet.def.atmos) }}</span>
        </div>
        <div class="row gx-2">
            <span class="col text-normal">Orbital Distance</span>
            <span class="col-auto text-white">{{ planet.def.orbit }} AU</span>
        </div>
        <div class="row gx-2">
            <span class="col text-normal">Influence</span>
            <span class="col-auto text-white">{{ planet.def.influence.toLocaleString() }}</span>
        </div>
    </div>
</template>

<script>
export default {
    props: [ 'planet' ],
}
</script>
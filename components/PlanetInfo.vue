<template>
    <div class="col-12">
        <div class="row gx-2">
            <span class="col text-normal">Type</span>
            <span class="col-auto text-white">{{ $t('planetType_' + planet.ref.env) }}</span>
        </div>
        <div class="row gx-2">
            <span class="col text-normal">Radius</span>
            <span class="col-auto text-white">{{ planet.ref.radius.toLocaleString() }} km</span>
        </div>
        <div class="row gx-2">
            <span class="col text-normal">Temperature</span>
            <span class="col-auto text-white">{{ planet.ref.temp }} °C</span>
        </div>
        <div class="row gx-2">
            <span class="col text-normal">Atmosphere</span>
            <span class="col-auto text-white">{{ $t('planetAtmos_' + planet.ref.atmos) }}</span>
        </div>
        <div class="row gx-2">
            <span class="col text-normal">Orbital Distance</span>
            <span class="col-auto text-white">{{ planet.ref.orbit }} AU</span>
        </div>
        <div class="row gx-2">
            <span class="col text-normal">Influence</span>
            <span class="col-auto text-white">{{ planet.ref.influence.toLocaleString() }}</span>
        </div>
    </div>
</template>

<script>
export default {
    props: [ 'planet' ],
}
</script>
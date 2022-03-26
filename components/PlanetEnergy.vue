<template>
    <div class="col-12">
        <div class="row gx-2">
            <span class="col text-normal">Energy Production</span>
            <span class="col-auto" :class="{ 'text-gray':planet.getEnergyProd() == 0, 'text-white':planet.getEnergyProd() > 0 }">{{ planet.getEnergyProd().toLocaleString() }} <small class="opacity-75">/s</small></span>
        </div>
        <div class="row gx-2">
            <span class="col text-normal">Energy Consumption</span>
            <span class="col-auto" :class="{ 'text-gray':planet.getEnergyConsum() == 0, 'text-white':planet.getEnergyConsum() > 0 }">{{ planet.getEnergyConsum().toLocaleString() }} <small class="opacity-75">/s</small></span>
        </div>
        <div class="row gx-2">
            <span class="col text-normal">Balance</span>
            <span class="col-auto" :class="{ 'text-success':planet.getEnergyProd() - planet.getEnergyConsum() > 0, 'text-gray':planet.getEnergyProd() - planet.getEnergyConsum() == 0, 'text-danger':planet.getEnergyProd() - planet.getEnergyConsum() < 0 }"><span v-if="planet.getEnergyProd() - planet.getEnergyConsum() > 0">+</span>{{ (planet.getEnergyProd() - planet.getEnergyConsum()).toLocaleString() }} <small class="opacity-75">/s</small></span>
        </div>
        <div class="row gx-2">
            <span class="col text-normal">Efficiency</span>
            <span class="col-auto" :class="{ 'text-success':planet.getEnergyCoeff() == 1, 'text-warning':planet.getEnergyCoeff() < 1 && planet.getEnergyCoeff() > .85, 'text-danger':planet.getEnergyCoeff() < .85 }">{{ Math.floor(1e4 * planet.getEnergyCoeff()) / 100 }} <small class="opacity-75">%</small></span>
        </div>
    </div>
</template>

<script>
export default {
    props: [ 'planet' ],
}
</script>
<template>
    <div class="col-12">
        <div class="row gx-2">
            <span class="col text-normal">Energy Production</span>
            <span class="col-auto" :class="{ 'text-gray':planet.energy.prod == 0, 'text-white':planet.energy.prod != 0 }">{{ planet.energy.prod.toLocaleString() }} <small class="opacity-75">/s</small></span>
        </div>
        <div class="row gx-2">
            <span class="col text-normal">Energy Consumption</span>
            <span class="col-auto" :class="{ 'text-gray':planet.energy.consum == 0, 'text-white':planet.energy.consum != 0 }">{{ planet.energy.consum.toLocaleString() }} <small class="opacity-75">/s</small></span>
        </div>
        <div class="row gx-2">
            <span class="col text-normal">Balance</span>
            <span class="col-auto" :class="{ 'text-success':balance > 0, 'text-gray':balance == 0, 'text-danger':balance < 0 }"><span v-if="balance > 0">+</span>{{ balance.toLocaleString() }} <small class="opacity-75">/s</small></span>
        </div>
        <div class="row gx-2">
            <span class="col text-normal">Efficiency</span>
            <span class="col-auto" :class="{ 'text-success':planet.getEnergyCoeff() == 1, 'text-warning':planet.getEnergyCoeff() < 1 && planet.getEnergyCoeff() > .85, 'text-danger':planet.getEnergyCoeff() < .85 }">{{ Math.floor(1e4 * planet.getEnergyCoeff()) / 100 }} <small class="opacity-75">%</small></span>
        </div>
        <div class="mt-3 row gx-2">
            <span class="col text-normal">Research Point</span>
            <span class="col-auto" :class="{ 'text-success':planet.researchPoint.prod > 0, 'text-gray':planet.researchPoint.prod <= 0}">+{{ planet.researchPoint.prod }} <small class="opacity-75">/s</small></span>
        </div>
    </div>
</template>

<script>
export default {
    props: [ 'planet' ],
    
    computed: {
    
        balance() { return this.planet.energy.prod + this.planet.energy.consum },
    },
}
</script>
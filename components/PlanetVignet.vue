<template>
    <div class="text-center">
        <div class="h5" :class="{ 'text-normal':planet.civId == 'human', 'text-danger':planet.civId != null && planet.civId != 'human', 'text-gray':planet.civId == null }" >{{ $t('planetName_' + planet.id) }}</div>
        <div class="row gx-0 align-items-center">
            <button v-if="planet.civId == 'human' && $parent.humanPlanetCount > 1" type="button" class="col-auto btn"><i class="fas fa-fw fa-chevron-left"></i></button>
            <button type="button" class="col btn py-0" @click="$parent.showPlanetPage(planet)"><img width="160px" :src="require(`~/assets/planets/${planet.id}.png`)" /></button>
            <button v-if="planet.civId == 'human' && $parent.humanPlanetCount > 1" type="button" class="col-auto btn"><i class="fas fa-fw fa-chevron-right"></i></button>
        </div>                                
    </div>
</template>

<script>
export default {

    props: [ 'planet' ],
}
</script>
<template>
    <div class="text-center">
        <div class="h5" :class="{ 'text-normal':planet.ref.civId == 'human', 'text-danger':planet.ref.civId != null && planet.ref.civId != 'human', 'text-gray':planet.ref.civId == null }" >{{ $t('planetName_' + planet.ref.id) }}</div>
        <div class="row gx-0 align-items-center">
            <button v-if="Object.keys($parent.game.getHumanPlanets()).length > 1" type="button" class="col-auto btn" @click="$parent.currentPlanetPrevious()"><i class="fas fa-fw fa-chevron-left"></i></button>
            <button type="button" class="col btn py-0" @click="$parent.showPlanetPage(planet)"><img width="130px" :src="require(`~/assets/planets/${planet.ref.id}.png`)" /></button>
            <button v-if="Object.keys($parent.game.getHumanPlanets()).length > 1" type="button" class="col-auto btn" @click="$parent.currentPlanetNext()"><i class="fas fa-fw fa-chevron-right"></i></button>
        </div>                                
    </div>
</template>

<script>
export default {
    props: [ 'planet' ],
}
</script>
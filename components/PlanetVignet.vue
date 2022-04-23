<template>
    <div class="text-center">
        <div class="h5" :class="{ 'text-normal':planet.def.civId == 'human', 'text-danger':planet.def.civId != null && planet.def.civId != 'human', 'text-gray':planet.def.civId == null }" >{{ $t('planetName_' + planet.def.id) }}</div>
        <div class="row gx-0 align-items-center">
            <button type="button" class="col-auto btn"><i class="fas fa-fw fa-chevron-left"></i></button>
            <button type="button" class="col btn py-0" @click="$parent.showPlanetPage(planet)"><img width="130px" :src="require(`~/assets/planets/${planet.def.id}.png`)" /></button>
            <button type="button" class="col-auto btn"><i class="fas fa-fw fa-chevron-right"></i></button>
        </div>                                
    </div>
</template>

<script>
export default {
    props: [ 'planet' ],
}
</script>
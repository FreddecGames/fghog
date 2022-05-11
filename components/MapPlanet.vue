<template>
     <button type="button" class="position-absolute btn" :style="'top:' + planet.ref.y + 'px; left:' + planet.ref.x + 'px;'" @click="$parent.showPlanetPage(planet)">
         <div class="row gx-1 align-items-center">
             <div class="col-auto"><img :src="require(`~/assets/planets/${planet.ref.id}.png`)" width="24px" /></div>
             <div class="col-auto"><span class="shadow-normal" :class="{ 'text-gray':planet.ref.civId == null, 'text-danger':planet.ref.civId != null && planet.ref.civId != 'human' }">{{ $t('planetName_' + planet.ref.id) }}</span></div>
         </div>
     </button>
</template>

<script>
export default {

    props: [ 'planet' ],
}
</script>
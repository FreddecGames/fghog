<template>
     <button type="button" class="position-absolute btn" :style="'top:' + planet.def.y + 'px; left:' + planet.def.x + 'px;'" @click="$parent.showPlanetPage(planet)">
         <div class="row gx-2 align-items-center">
             <div class="col-auto"><!-- img :src="require(`~/assets/planets/${planet.def.id}.png`)" width="24px" /--></div>
             <div class="col-auto"><span class="text-shadow">{{ $t('planetName_' + planet.def.id) }}</span></div>
         </div>
     </button>
</template>

<script>
export default {

    props: [ 'planet' ],
}
</script>
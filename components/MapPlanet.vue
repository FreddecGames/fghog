<template>
     <button type="button" class="position-absolute btn" :style="'top:' + planet.y + 'px; left:' + planet.x + 'px;'" @click="$parent.showPlanetPage(planet)">
         <div class="row gx-2 align-items-center">
             <div class="col-auto"><img :src="require(`~/assets/planets/${planet.id}.png`)" width="24px" /></div>
             <div class="col-auto"><span>{{ $t('planetName_' + planet.id) }}</span></div>
         </div>
     </button>
</template>

<script>
export default {

    props: [ 'planet' ],
}
</script>
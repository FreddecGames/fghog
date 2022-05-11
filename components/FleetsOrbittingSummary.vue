<template>
    <button type="button" class="w-100 btn btn-hover border-bottom rounded-0" :class="{ 'bg-2':$parent.currentFleetsOrbittingIndex == index }" @click="$parent.setCurrentFleetsOrbitting(index, fleet)">
        <div class="row gx-2 align-items-center">
            <div class="col-auto"><img :src="require(`~/assets/planets/${fleet.planet.ref.id}.png`)" width="24px" /></div>
            <div class="col text-start" :class="{ 'text-primary':fleet.planet.ref.civId == 'human', 'text-danger':fleet.planet.ref.civId != 'human' && fleet.planet.ref.civId != null, 'text-gray':fleet.planet.ref.civId == null }">{{ $t('planetName_' + fleet.planet.ref.id) }}</div>
            <div class="col-auto text-white text-start">{{ fleet.name }}</div>
        </div>
    </button>
</template>

<script>
export default {

    props: [ 'index', 'fleet' ],    
}
</script>
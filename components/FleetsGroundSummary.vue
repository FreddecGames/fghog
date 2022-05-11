<template>
    <button type="button" class="w-100 btn btn-hover border-bottom rounded-0" :class="{ 'bg-2':$parent.currentFleetsGround && $parent.currentFleetsGround.ref.id == planet.ref.id }" @click="$parent.setCurrentFleetsGround(planet)">
        <div class="row gx-2 align-items-center">
            <div class="col-auto"><img :src="require(`~/assets/planets/${planet.ref.id}.png`)" width="24px" /></div>
            <div class="col text-primary text-start">{{ $t('planetName_' + planet.ref.id) }}</div>
            <div class="col-auto">
                <span :class="{ 'text-white':planet.getGroundShipCount() > 0, 'text-gray':planet.getGroundShipCount() <= 0 }">{{ planet.getGroundShipCount().toLocaleString() }}</span>
                <span :class="{ 'text-normal':planet.getGroundShipCount() > 0, 'text-gray':planet.getGroundShipCount() <= 0 }">ships</span>
            </div>
        </div>
    </button>
</template>

<script>
export default {

    props: [ 'planet' ],    
}
</script>
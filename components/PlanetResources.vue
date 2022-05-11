<template>
    <div class="col-12">
        <div v-for="(res, key) of planet.getUnlockedResources()" :key="key" class="row gx-2 align-items-baseline">
            <span class="col text-normal">{{ $t('resName_' + key) }}</span>
            <span class="col-auto" :class="{ 'text-white':res.count >= 1, 'text-gray':res.count < 1 }"><FormatNumber :value="res.count" /></span>
            <small class="col-auto text-end" :class="{ 'text-success':res.prod > 0, 'text-gray':res.prod == 0, 'text-danger':res.prod < 0 }" style="width:60px;"><span v-if="res.prod > 0"><small class="opacity-75">+</small></span><FormatNumber :value="res.prod" /> <small class="opacity-75">/s</small></small>
        </div>
    </div>
</template>

<script>
export default {

    props: [ 'planet' ],
}
</script>
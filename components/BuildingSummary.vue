<template>
    <button type="button" class="w-100 btn btn-hover border-bottom rounded-0" :class="{ 'bg-2':$parent.currentBuilding && $parent.currentBuilding.ref.id == building.ref.id }" @click="$parent.setCurrentBuilding(building)">
        <div class="row gx-2 align-items-center">
            <button v-if="building.ref.prod || building.ref.energy || building.ref.researchPoint" type="button" class="col-auto btn btn-sm" :class="{ 'disabled':building.count < 1 }" @click="building.toggleActive()">
                <i class="fas fa-fw fa-power-off" :class="{ 'text-danger':building.active == false, 'text-success': building.active == true }"></i>
            </button>
            <span class="col-auto text-primary">{{ $t('buildingName_' + building.ref.id) }}</span>
            <span class="col-auto" :class="{ 'text-white':building.count > 0, 'text-gray':building.count <= 0 }">{{ building.count.toLocaleString() }}</span>
            <span class="col text-start">
                <span v-if="building.hasNeed() == true" class="medium text-danger">
                    <i class="fas fa-fw fa-exclamation-triangle"></i> <span v-for="(res, key) of building.need" :key="key"><span v-if="building.need[key] == true">{{ $t('resName_' + key) }}</span></span>
                </span>
            </span>
            <div v-if="building.queue > 0" class="col-auto">
                <button type="button" class="col-auto btn btn-sm" @click="building.emptyQueue()">
                    <span>+{{ building.queue }} in queue</span>
                    <i class="fas fa-fw fa-times-circle"></i>
                </button>
            </div>
            <div class="col-auto btn-group btn-group-sm">
                <button type="button" class="btn" :class="{ 'text-success':building.canBuild(1) }" @click="building.addQueue(1)"><i class="fas fa-fw fa-plus-circle"></i>1</button>
                <button type="button" class="btn" :class="{ 'text-success':building.canBuild(10) }" @click="building.addQueue(10)"><i class="fas fa-fw fa-plus-circle"></i>10</button>
                <button type="button" class="btn" :class="{ 'text-success':building.canBuild(50) }" @click="building.addQueue(50)"><i class="fas fa-fw fa-plus-circle"></i>50</button>
            </div>
        </div>
    </button>
</template>

<script>
export default {

    props: [ 'building' ],
}
</script>
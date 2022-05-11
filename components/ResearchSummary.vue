<template>
    <button type="button" class="w-100 btn rounded bg-1 border" :class="{ 'bg-2 text-white':$parent.currentResearch && $parent.currentResearch.ref.id == tech.ref.id }" @click="$parent.setCurrentResearch(tech)">
        <div v-if="tech.isUnlocked() == false">
            <div class="d-flex justify-content-center align-items-center" style="height:36px;">
                <span class="text-gray" style="width:85px;">{{ $t('techName_' + tech.ref.id) }}</span>
            </div>
            <div class="px-2 mt-2 mb-1">
                <div class="row gx-2">
                    <div class="col small text-gray text-center"><i class="fas fa-fw fa-lock"></i> Locked</div>
                </div>
            </div>
        </div>
        <div v-if="tech.isUnlocked() == true">
            <div class="d-flex justify-content-center align-items-center" style="height:36px;">
                <span style="width:85px;">{{ $t('techName_' + tech.ref.id) }}</span>
            </div>
            <div class="mt-2">
                <div class="row gx-2 align-items-center">
                    <div class="col text-start">
                        <span class="text-normal small">Lvl</span>
                        <span class="ms-1 text-white">{{ tech.level }}</span>
                    </div>
                    <button type="button" class="col-auto btn btn-sm p-0" :class="{ 'disabled':tech.canLevelUpRP() == false, 'text-success':tech.canLevelUpRP() == true }" @click="tech.levelUpRP()">
                        <i class="fas fa-fw fa-arrow-circle-up"></i>
                        Level up
                    </button>
                </div>
            </div>
        </div>
    </button>
</template>

<script>
export default {

    props: [ 'tech' ],
}
</script>
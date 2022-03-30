<template>
    <button type="button" class="w-100 btn rounded bg-1 border" :class="{ 'bg-2 text-white':$parent.currentResearch && $parent.currentResearch.id == research.id }" @click="$parent.setCurrentResearch(research)">
        <div v-if="$parent.isResearchUnlocked(research.id) == false">
            <div class="d-flex justify-content-center align-items-center" style="height:36px;">
                <span class="text-gray" style="width:85px;">{{ $t('researchName_' + research.id) }}</span>
            </div>
            <div class="px-2 mt-2 mb-1">
                <div class="row gx-2">
                    <div class="col small text-gray text-center"><i class="fas fa-fw fa-lock"></i> Locked</div>
                </div>
            </div>
        </div>
        <div v-if="$parent.isResearchUnlocked(research.id) == true">
            <div class="d-flex justify-content-center align-items-center" style="height:36px;">
                <span style="width:85px;">{{ $t('researchName_' + research.id) }}</span>
            </div>
            <div class="px-2 mt-2">
                <div class="row gx-2 align-items-center">
                    <div class="col text-start">
                        <span class="text-normal small">Lvl</span>
                        <span class="ms-1 text-white">{{ research.level }}</span>
                    </div>
                    <button type="button" class="col-auto btn btn-sm p-0" :class="{ 'disabled':$parent.canLevelUp(research.id) == false, 'text-success':$parent.canLevelUp(research.id) == true }" @click="$parent.levelUpResearch(research.id)">
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

    props: [ 'research' ],
}
</script>
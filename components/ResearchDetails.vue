<template>
    <div>
        <div v-if="tech.isUnlocked() == false" class="row g-3">
            <div class="col-12 text-center">
                <div class="h5 text-gray">{{ $t('techName_' + tech.ref.id) }}</div>
                <div class="small text-gray"><i class="fas fa-fw fa-lock"></i> Locked</div>
            </div>
            <div v-if="tech.ref.desc == true" class="col-12 mt-4 text-center">
                <span class="text-warning">{{ $t('techDesc_' + tech.ref.id) }}</span>
            </div>
            <div class="col-12 mt-4">
                <div class="row align-items-center gx-2 mb-1">
                    <div class="col-auto h6 text-gray mb-2">
                        Requirements
                    </div>
                </div>
                <div v-for="(req, key) of tech.ref.techReqs" :key="key">
                    <div class="row gx-2 align-items-baseline">
                        <span class="col text-normal">{{ $t('techName_' + key) }}</span>
                        <span class="col-auto small" :class="{ 'text-success':$parent.game.techs[key].level >= req, 'text-normal':$parent.game.techs[key].level < req }">Lvl</span>
                        <span class="col-auto" :class="{ 'text-success':$parent.game.techs[key].level >= req, 'text-white':$parent.game.techs[key].level < req }">{{ req }}</span>
                    </div>
                </div>
            </div>
            <div class="col-12 mt-4" v-html="tech.getDescription()"></div>
        </div>
        <div v-if="tech.isUnlocked() == true" class="row g-3">
            <div class="col-12 text-center">
                <div class="h5 text-primary">{{ $t('techName_' + tech.ref.id) }}</div>
                <div class="small text-gray"><span class="text-normal">Lvl</span> <span class="ms-1 text-white">{{ tech.level }}</span> <span v-if="tech.ref.max > 0" class="">/{{ tech.ref.max }}</span></div>
            </div>
            <div v-if="tech.ref.desc == true" class="col-12 mt-4 text-center">
                <span class="text-warning">{{ $t('techDesc_' + tech.ref.id) }}</span>
            </div>
            <div class="col-12 mt-4">
                <div class="row gx-2 align-items-baseline">
                    <div class="col">
                        <span class="text-normal">Research Points</span>
                    </div>
                    <div class="col-auto">
                        <span :class="{ 'text-danger':tech.canLevelUpRP() == false, 'text-success':tech.canLevelUpRP() == true }"><FormatNumber :value="tech.getCostRP()" /></span>
                    </div>
                </div>
                <div class="mt-2">
                    <button type="button" class="w-100 btn border" :class="{ 'disabled':tech.canLevelUpRP() == false }" @click="tech.levelUpRP()">Level up</button>
                </div>
            </div>
            <div class="col-12 mt-4" v-html="tech.getDescription()"></div>
        </div>
    </div>
</template>

<script>
export default {

    props: [ 'tech' ],
}
</script>
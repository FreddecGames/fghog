<template>
    <button type="button" class="w-100 btn btn-hover border-bottom rounded-0" :class="{ 'bg-2':$parent.currentTravelIndex == index }" @click="$parent.setCurrentTravel(index, travel)">
        <div class="row gx-2 align-items-center">
            <div class="col text-white text-start">{{ travel.fleet.name }}</div>
            <div class="col-auto">
                <div class="row gx-0 align-items-center">
                    <div class="col-auto"><img :src="require(`~/assets/planets/${travel.origin.ref.id}.png`)" width="24px"></div>
                    <div class="col-auto"><span>{{ $t('planetName_' + travel.origin.ref.id) }}</span></div>
                </div>
            </div>
            <div v-if="travel.type == 'normal'" class="col-auto">
                <i class="text-gray fas fa-fw fa-long-arrow-alt-right"></i>
            </div>
            <div v-if="travel.type == 'autoroute'" class="col-auto">
                <div class="row gx-2 align-items-center">
                    <div class="col-auto">
                        <i class="text-gray fas fa-fw fa-long-arrow-alt-left"></i>
                    </div>
                    <div class="col-auto">
                        <span class="text-gray medium">Autoroute</span>
                    </div>
                    <div class="col-auto">
                        <i class="text-gray fas fa-fw fa-long-arrow-alt-right"></i>
                    </div>
                </div>
            </div>
            <div class="col-auto">
                <div class="row gx-0 align-items-center">
                    <div class="col-auto"><img :src="require(`~/assets/planets/${travel.destination.ref.id}.png`)" width="24px"></div>
                    <div class="col-auto"><span>{{ $t('planetName_' + travel.destination.ref.id) }}</span></div>
                </div>
            </div>
            <div class="col-auto text-end" style="width:75px;">
                <FormatTime v-if="travel.remainingSeconds > 0" :value="travel.remainingSeconds" />
                <span v-if="travel.remainingSeconds <= 0" class="text-success">Arrived</span>
            </div>
        </div>
    </button>
</template>

<script>
export default {

    props: [ 'index', 'travel' ],
}
</script>
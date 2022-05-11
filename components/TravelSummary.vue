<template>
    <button type="button" class="w-100 btn btn-hover border-bottom rounded-0" :class="{ 'bg-2':$parent.currentTravelIndex == index }" @click="$parent.setCurrentTravel(index, travel)">
        <div class="row gx-2 align-items-center">
            <div class="col text-white text-start">{{ travel.fleet.name }}</div>
            <div class="col-auto">
                <div class="row gx-0 align-items-center">
                    <div class="col-auto"><img :src="require(`~/assets/planets/${travel.source.ref.id}.png`)" width="24px"></div>
                    <div class="col-auto"><span>{{ $t('planetName_' + travel.source.ref.id) }}</span></div>
                </div>
            </div>
            <div class="col-auto">
                <i class="text-gray fas fa-fw fa-long-arrow-alt-right"></i>
            </div>
            <div class="col-auto">
                <div class="row gx-0 align-items-center">
                    <div class="col-auto"><img :src="require(`~/assets/planets/${travel.destination.ref.id}.png`)" width="24px"></div>
                    <div class="col-auto"><span>{{ $t('planetName_' + travel.destination.ref.id) }}</span></div>
                </div>
            </div>
            <div class="col-auto text-end" style="width:75px;">
                <FormatTime v-if="remainingTime > 0" :value="remainingTime" />
                <span v-if="remainingTime <= 0" class="text-success">Arrived</span>
            </div>
        </div>
    </button>
</template>

<script>
export default {

    props: [ 'index', 'travel' ],
    
    data() {
        return {
            
            remainingTime: Math.max(0, (this.travel.arrivalTime - (new Date().getTime())) / 1000),
        }
    },
    
    created() {
        
        const self = this
        setInterval(function() {
            
            self.remainingTime = Math.max(0, (self.travel.arrivalTime - (new Date().getTime())) / 1000)
        })
    },
}
</script>
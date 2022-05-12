<template>
    <div>
        <button v-if="ship.isUnlocked() == true" type="button" class="w-100 btn btn-hover border-bottom rounded-0" :class="{ 'bg-2':$parent.currentShip && $parent.currentShip.ref.id == ship.ref.id }" @click="$parent.setCurrentShip(ship)">
            <div class="row gx-2 align-items-center">
                <button v-if="ship.prods" type="button" class="col-auto btn btn-sm" :class="{ 'disabled':ship.count < 1 }" @click="ship.toggleActive()">
                    <i class="fas fa-fw fa-power-off" :class="{ 'text-danger':ship.active == false, 'text-success': ship.active == true }"></i>
                </button>
                <span class="col-auto text-primary">{{ $t('shipName_' + ship.ref.id) }}</span>
                <span class="col text-start" :class="{ 'text-white':ship.count > 0, 'text-gray':ship.count <= 0 }">{{ ship.count.toLocaleString() }}</span>
                <div class="col-auto btn-group btn-group-sm">
                    <button type="button" class="btn" :class="{ 'disabled':$parent.currentPlanet.canBuy(ship.getCost(1)) == false  }" @click="ship.build(1)"><i class="fas fa-fw fa-plus-circle"></i>1</button>
                    <button type="button" class="btn" :class="{ 'disabled':$parent.currentPlanet.canBuy(ship.getCost(10)) == false }" @click="ship.build(10)"><i class="fas fa-fw fa-plus-circle"></i>10</button>
                    <button type="button" class="btn" :class="{ 'disabled':$parent.currentPlanet.canBuy(ship.getCost(100)) == false }" @click="ship.build(100)"><i class="fas fa-fw fa-plus-circle"></i>100</button>
                    <button type="button" class="btn" :class="{ 'disabled':$parent.currentPlanet.canBuy(ship.getCost(1000)) == false }" @click="ship.build(1000)"><i class="fas fa-fw fa-plus-circle"></i>1<small class="opacity-75"> K</small></button>
                    <button type="button" class="btn" :class="{ 'disabled':$parent.currentPlanet.canBuy(ship.getCost(10000)) == false }" @click="ship.build(10000)"><i class="fas fa-fw fa-plus-circle"></i>10<small class="opacity-75"> K</small></button>
                </div>
            </div>
        </button>
        <button v-if="ship.isUnlocked() == false" type="button" class="w-100 btn btn-hover border-bottom rounded-0 py-2 text-start" :class="{ 'bg-2':$parent.currentShip && $parent.currentShip.ref.id == ship.ref.id }" @click="$parent.setCurrentShip(ship)">
            <div class="row gx-2 align-items-center">
                <div class="col">
                    <span class="text-danger">{{ $t('shipName_' + ship.ref.id) }}</span>
                </div>
                <div class="col-auto">
                    <span class="medium text-danger">{{ ship.ref.shipyardLevel }} Shipyards required</span>
                </div>
            </div>
        </button>
    </div>
</template>

<script>
export default {

    props: [ 'ship' ],
}
</script>
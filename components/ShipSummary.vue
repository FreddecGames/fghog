<template>
    <button type="button" class="w-100 btn btn-hover border-bottom rounded-0" :class="{ 'bg-2':$parent.currentShip && $parent.currentShip.id == ship.id }" @click="$parent.setCurrentShip(ship)">
        <div class="row gx-2 align-items-center">
            <button v-if="ship.prods" type="button" class="col-auto btn btn-sm" :class="{ 'disabled':ship.count < 1 }" @click="ship.toggleActive()">
                <i class="fas fa-fw fa-power-off" :class="{ 'text-danger':ship.active == false, 'text-success': ship.active == true }"></i>
            </button>
            <span class="col-auto text-primary">{{ $t('shipName_' + ship.id) }}</span>
            <span class="col text-start" :class="{ 'text-white':ship.count > 0, 'text-gray':ship.count <= 0 }">{{ ship.count.toLocaleString() }}</span>
            <div class="col-auto">
                <button type="button" class="btn btn-sm" :class="{ 'disabled':$parent.currentPlanet.canBuy($parent.currentPlanet.getShipCost(ship.id, 1)) == false  }" @click="$parent.currentPlanet.buildShip(ship.id, 1)"><i class="fas fa-fw fa-plus-circle"></i>1</button>
                <button type="button" class="btn btn-sm" :class="{ 'disabled':$parent.currentPlanet.canBuy($parent.currentPlanet.getShipCost(ship.id, 10)) == false }" @click="$parent.currentPlanet.buildShip(ship.id, 10)"><i class="fas fa-fw fa-plus-circle"></i>10</button>
                <button type="button" class="btn btn-sm" :class="{ 'disabled':$parent.currentPlanet.canBuy($parent.currentPlanet.getShipCost(ship.id, 50)) == false }" @click="$parent.currentPlanet.buildShip(ship.id, 50)"><i class="fas fa-fw fa-plus-circle"></i>50</button>
            </div>
        </div>
    </button>
</template>

<script>
export default {

    props: [ 'ship' ],
}
</script>
<template>
     <div class="position-absolute bg-normal border" :style="'top:' + y + 'px; left:' + x + 'px; width:' + route.distance() + 'px; height:4px; transform: rotate(' + angle + 'deg); transform-origin:top left;'">
     </div>
</template>

<script>
export default {

    props: [ 'route' ],
    
    computed: {
    
        x() {
        
            let ret = 25 + this.route.planet1.ref.x
            if (this.angle > 90) ret -= 2
            else ret += 2
            
            return ret
        },
        
        y() {
        
            let ret = 17 + this.route.planet1.ref.y
            if (this.angle > 90) ret += 2
            else ret -= 2
            
            return ret
        },
        
        angle() {
        
            let ret = 180 * Math.atan(this.route.cy() / this.route.cx()) / Math.PI
            if (0 > this.route.cy() && 0 > ret) ret += 180
            return ret
        },
    },
}
</script>
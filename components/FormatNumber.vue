<template>
    <span>{{ format.value }}<small v-if="format.symbol" class="opacity-75"> {{ format.symbol }}</small></span>
</template>

<script>
export default {
    props: [ 'value' ],
    computed: {
    
        format: function() {
            
            let sign = ""
            if (this.value < 0) sign = "-"
            
            let absValue = Math.floor(Math.abs(this.value)) || 0
            
            let ret = "", symbol = null
            
                 if (absValue >= 1e36) { ret = sign + (absValue / 1e36).toFixed(1); symbol = "Ud"; }
            else if (absValue >= 1e33) { ret = sign + (absValue / 1e33).toFixed(1); symbol = "Dc"; }
            else if (absValue >= 1e30) { ret = sign + (absValue / 1e30).toFixed(1); symbol = "No"; }
            else if (absValue >= 1e27) { ret = sign + (absValue / 1e27).toFixed(1); symbol = "Oc"; }
            else if (absValue >= 1e24) { ret = sign + (absValue / 1e24).toFixed(1); symbol = "Sp"; }
            else if (absValue >= 1e21) { ret = sign + (absValue / 1e21).toFixed(1); symbol = "Sx"; }
            else if (absValue >= 1e18) { ret = sign + (absValue / 1e18).toFixed(1); symbol = "Qi"; }
            else if (absValue >= 1e15) { ret = sign + (absValue / 1e15).toFixed(1); symbol = "Qa"; }
            else if (absValue >= 1e12) { ret = sign + (absValue / 1e12).toFixed(1); symbol = "T";  }
            else if (absValue >= 1e9)  { ret = sign + (absValue / 1e9).toFixed(1);  symbol = "B";  }
            else if (absValue >= 1e6)  { ret = sign + (absValue / 1e6).toFixed(1);  symbol = "M";  }
            else if (absValue >= 1e3)  { ret = sign + (absValue / 1e3).toFixed(1);  symbol = "K";  }
            else if (absValue >= 100)  { ret = sign + Math.abs(this.value).toFixed(1); }
            
            else ret = sign + Math.abs(this.value).toFixed(2)
            
            return { value:ret, symbol:symbol }
        }
    },
}
</script>
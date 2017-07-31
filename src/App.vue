<template>
<div>
    <calculator
        :cal-num-options="{'basic':true, 'science':false, 'programmer':false}"
        :cal-unit-options="true">
    </calculator>
</div>
</template>

<script>
import Calculator from './Calculator.vue'

export default{
    components:{Calculator}
}
</script>
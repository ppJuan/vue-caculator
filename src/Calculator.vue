<template>
    <div id="calculator">
        <header>
            <select v-model="menu" >
                <option value="basic"       selected                        >标准</option>
                <option value="science"     v-if="calNumOptions.science"    >科学</option>
                <option value="programmer"  v-if="calNumOptions.programmer" >程序员</option>
                <option value="volume"      v-if="calUnitOptions"           >体积</option>
                <option value="weight"      v-if="calUnitOptions"           >重量</option>
                <option value="length"      v-if="calUnitOptions"           >长度</option>
                <option value="angle"       v-if="calUnitOptions"           >角度</option>
                <option value="temperature" v-if="calUnitOptions"           >温度</option>
            </select>
            <p>PP's Calculator 2017-07-05</p>
        </header>
        <cal-num
                :menu="menu"
                v-if="mode==='calNum' ? true : false">
        </cal-num>
        <cal-unit
                :menu="menu"
                v-if="mode==='calUnit' ? true : false">
        </cal-unit>
    </div>
</template>
<script>
import CalNum from './CalNum.vue'
import CalUnit from './CalUnit.vue'

export default{
    props: {
        'calNumOptions':{
            type: Object,
            default: {'basic':true, 'science':false, 'programmer': false}
        },
        'calUnitOptions':{
            type: Boolean,
            default: false
        }
    },
    data (){
        return{
            menu: 'basic'
        };
    },
    computed:{
        mode (){
            if(this.menu==='basic')
                return 'calNum';
            if(this.menu==='volume' ||
                this.menu==='weight' ||
                this.menu==='length' ||
                this.menu==='angle' ||
                this.menu==='temperature')
                return 'calUnit';
        }
    },
    components:{
        CalNum,
        CalUnit
    }
}
</script>

<style>
    #calculator{
        position: absolute;
        width:300px;
        top:50%;
        left:50%;
        transform: translate(-50%,-50%);
        border: 1px solid black;
    }
    #calculator header{
        width: 100%;
        text-align: right;
    }
    #calculator header p{
        color: darkgrey;
        font-size: 10px;
        padding-right: 5px;
    }
    #calculator header select{
        position: absolute;
        top:2px;
        left:2px;
        border: 0;
    }
</style>
<template>
    <div>
        <div id="panel">
            <hr>
            <!------------------------------------------------显示板1-------------------------------------------->
            <div
              :class="{'text-indicate': !panelInputIndex}"
              @click="toggleInput(0)">
                {{inputData[0]}}
            </div>
            <!------------------------------------------------选择列表1-------------------------------------------->
            <select @change="selectChange($event,0)">

                <option
                  v-for="item in units[menu]"
                  :value="item.ref">
                    {{item.text}}
                </option>

            </select>
            <hr>
            <!------------------------------------------------显示板2-------------------------------------------->
            <div
              :class="{'text-indicate': panelInputIndex}"
              @click="toggleInput(1)">
                {{inputData[1]}}
            </div>

            <!------------------------------------------------选择列表2-------------------------------------------->
            <select @change="selectChange($event,1)">

                <option
                  v-for="item in units[menu]"
                  :value="item.ref">
                    {{item.text}}
                </option>

            </select>
            <hr>
        </div>

        <!------------------------------------------------输入板-------------------------------------------->

        <div id="board" @click="inputHandler($event)">
            <table>
                <tr>
                    <td data-type="" data-value=""></td>
                    <td data-type="opr0" data-value="CE">CE</td>
                    <td data-type="opr0" data-value="backspace">&#8592</td>
                </tr>
                <tr>
                    <td data-type="num" data-value="7">7</td>
                    <td data-type="num" data-value="8">8</td>
                    <td data-type="num" data-value="9">9</td>
                </tr>
                <tr>
                    <td data-type="num" data-value="4">4</td>
                    <td data-type="num" data-value="5">5</td>
                    <td data-type="num" data-value="6">6</td>
                </tr>
                <tr>
                    <td data-type="num" data-value="1">1</td>
                    <td data-type="num" data-value="2">2</td>
                    <td data-type="num" data-value="3">3</td>
                </tr>
                <tr>
                    <td data-type="" data-value=""></td>
                    <td data-type="num" data-value="0">0</td>
                    <td data-type="num" data-value=".">.</td>
                </tr>
            </table>
        </div>
    </div>
</template>
<script>
    export default{
        props:{
            menu: String //菜单
        },
        data (){
            return {
                inputData: ['',''], //输入数据
                panelInputIndex: 0, //激活的输入板的索引值
                toggled: 0, //是否切换
                ref: [1, 1], //当前单位参考量
                units:{      //单位
                    volume:[
                        {text:'毫升', ref:1},
                        {text:'立方厘米', ref:1},
                        {text:'升', ref:1000},
                        {text:'立方米', ref:1000000},
                        {text:'茶匙(美制)', ref:4.928922},
                        {text:'汤匙(美制)', ref:14.78677},
                        {text:'液盎司(美制)', ref:29.57353},
                        {text:'杯(美制)', ref:236.5882},
                        {text:'品脱(美制)', ref:473.1765},
                        {text:'夸脱(美制)', ref:946.3529},
                        {text:'加仑(美制)', ref:3785.412},
                        {text:'立方英寸', ref:16.38706},
                        {text:'立方英尺', ref:28316.85},
                        {text:'立方码', ref:764554.9},
                        {text:'茶匙(英制)', ref:5.919388},
                        {text:'汤匙(英制)', ref:17.75816},
                        {text:'液盎司(英制)', ref:28.41306},
                        {text:'品脱(英制)', ref:568.2613},
                        {text:'夸脱(英制)', ref:1136.523},
                        {text:'加仑(英制)', ref:4546.09}
                    ],
                    weight:[
                        {text:'克拉', ref:200},
                        {text:'毫克', ref:1},
                        {text:'厘克', ref:10},
                        {text:'克', ref:1000},
                        {text:'千克', ref:1000000},
                        {text:'公吨', ref:1000000000},
                        {text:'盎司', ref:28349.52},
                        {text:'磅', ref:453592.4},
                        {text:'英石', ref:6350293},
                        {text:'短吨(美制)', ref:907184740},
                        {text:'长吨(英制)', ref:1016046909}
                    ],
                    length:[
                        {text:'纳米', ref:0.000001},
                        {text:'微米', ref:0.001},
                        {text:'毫米', ref:1},
                        {text:'厘米', ref:10},
                        {text:'分米', ref:100},
                        {text:'米', ref:1000},
                        {text:'千米', ref:1000000},
                        {text:'公里', ref:1000000},
                        {text:'里', ref:500000},
                        {text:'英寸', ref:25.4},
                        {text:'英尺', ref:304.8},
                        {text:'码', ref:914.4},
                        {text:'英里', ref:1609344},
                        {text:'海里', ref:1852000}
                    ],
                    angle:[
                        {text:'度', ref:1},
                        {text:'弧度', ref:57.29578},
                        {text:'梯度', ref:0.9}
                    ],
                    temperature:[
                        {text:'摄氏度', ref:1},
                        {text:'华氏度', ref:-17.22222},
                        {text:'开尔文', ref:-272.15}
                    ]
                }
            }
        },
        methods:{

            /** 初始化 */
            init(){
                this.inputData = ['', ''];
                this.toggled = 0;
            },

            /** 输入处理 */
            inputHandler(e){

                let type = e.target.dataset.type;
                let value = e.target.dataset.value;

                //输入板切换，重新计算
                if(this.toggled){
                    this.init();
                }

                //处理管理级操作符
                if(type==='opr0'){

                    switch (value){
                        case 'CE':
                            this.init();
                            break;
                        case 'backspace':
                            let i = this.panelInputIndex;
                            this.inputData.splice(i,1,this.inputData[i].slice(0,this.inputData[i].length-1));
                            this.convert(this.panelInputIndex);
                            break;
                        default:
                            break;
                    }
                }

                //处理数字
                else if (type==='num'){

                    this.inputData[this.panelInputIndex] += value;

                    this.convert(this.panelInputIndex);
                }
            },

            /** 转换函数 */
            convert(i){

                //转换
                let tmp;
                if(this.menu==='temperature'){
                    tmp = parseFloat(this.inputData[i]) + this.ref[i] - this.ref[1-i];
                }else{
                    tmp = parseFloat(this.inputData[i]) * this.ref[i] / this.ref[1-i];
                }

                //显示结果
                this.inputData.splice(1-i,1,tmp.toString());

                //错误处理
                if(!this.isNumber(tmp)){
                    this.init();
                }
            },

            /** 显示板切换 */
            toggleInput(index){

                if(index !== this.panelInputIndex){
                    this.panelInputIndex = index;
                    this.toggled = 1;
                }
            },

            /** 单位切换 */
            selectChange(e,index){
                debugger
                this.ref.splice(index, 1, parseFloat(e.currentTarget.value));
                if(this.inputData[this.panelInputIndex]!==''){
                    this.convert(this.panelInputIndex);
                }
            },

            /** 判断数值 */
            isNumber(value){
                return typeof value === 'number' && isFinite(value);
            }
        },
        watch: {

            /** 监听菜单切换 */
            menu: function(){
                debugger
                this.init();
                this.ref = [1, 1];
                for (let item of document.querySelectorAll("#panel select")){
                    item.value = "1";
                }
            }
        }
    }
</script>


<style scoped>
    #board{
        width: 100%;
        text-align: center;
    }
    #panel{
        width: 100%;
        text-align: left;
    }
    #panel div{
        height:auto;
        min-height: 22px;
        word-wrap: break-word;
        padding: 0 5px;
    }

    #board table{
        width: 100%;
    }
    #board table td{
        width: 33.33%;
        height: 40px;
    }
    #board table td:hover{
        background-color: darkgrey;
    }
    .text-indicate{
        color: dodgerblue;
    }

</style>
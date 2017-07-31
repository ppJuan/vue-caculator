<template>
  <div id="calNum">
    <div id="panel">
      <hr>
      <div>{{ cacheStr }}</div>
      <hr>
      <div :class="{'text-danger' : errorOccurred}">{{ inputData }}</div>
      <hr>
    </div>
    <div id="board" @click="changePanel($event);inputHandler($event)">
      <table>
        <tr>
          <td data-type="opr0" data-value="CE">CE</td>
          <td data-type="opr0" data-value="C">C</td>
          <td data-type="opr0" data-value="backspace">&#8592</td>
          <td data-type="opr2" data-value="divide" data-level = 1>&#247</td>
        </tr>
        <tr>
          <td data-type="num" data-value="7">7</td>
          <td data-type="num" data-value="8">8</td>
          <td data-type="num" data-value="9">9</td>
          <td data-type="opr2" data-value="multiply" data-level = 1>&#215</td>
        </tr>
        <tr>
          <td data-type="num" data-value="4">4</td>
          <td data-type="num" data-value="5">5</td>
          <td data-type="num" data-value="6">6</td>
          <td data-type="opr2" data-value="minus" data-level = 0>－</td>
        </tr>
        <tr>
          <td data-type="num" data-value="1">1</td>
          <td data-type="num" data-value="2">2</td>
          <td data-type="num" data-value="3">3</td>
          <td data-type="opr2" data-value="plus" data-level = 0>+</td>
        </tr>
        <tr>
          <td data-type="" data-value=""></td>
          <td data-type="num" data-value="0">0</td>
          <td data-type="num" data-value=".">.</td>
          <td data-type="opr1" data-value="equals" data-level = 2>=</td>
        </tr>
      </table>
    </div>

  </div>
</template>

<script>
  export default{
      data () {
          return{
              cacheArr:[], //显示算式
              inputData:'', //缓存输入数值
              preType:'num', //上一个输入类型
              oprStack:[], //操作符栈
              numStack:[], //操作数栈
              calculated:0, //连续计算和重新计算等逻辑需要的标志
              inputShow: 0, //inputData 有时作为输入有时作为输出，需要一个标志
              errorOccurred: 0 //错误标志

          }
      },
      computed:{
          cacheStr() {
              for(let i=0;i<this.cacheArr.length;i++){
                  if(this.cacheArr[i] === 'minus'){
                      this.cacheArr[i] = '\u2212';
                  }
                  if(this.cacheArr[i] === 'plus'){
                      this.cacheArr[i] = '\u002b';
                  }
                  if(this.cacheArr[i] === 'factorial'){
                      this.cacheArr[i] = '\u0021';
                  }
                  if(this.cacheArr[i] === 'divide'){
                      this.cacheArr[i] = '\u00f7';
                  }
                  if(this.cacheArr[i] === 'multiply'){
                      this.cacheArr[i] = '\u00d7';
                  }
                  if(this.cacheArr[i] === 'equals'){
                      this.cacheArr[i] = '';
                  }
              }
              return this.cacheArr.join("");
          }
      },
      methods: {
          /** 初始化 */
          init(){
              this.inputData = '';
              this.numStack = [];
              this.oprStack = [];
              this.cacheArr = [];
              this.preType = 'num';
              this.calculated = 0;
              this.inputShow = 0;
              this.errorOccurred = 0;
          },

          /** 显示处理 */
          changePanel(e){

              let type = e.target.dataset.type;
              let value = e.target.dataset.value;

              if(type==='opr2' || type==='opr1'){
                  if(this.inputData){
                      this.cacheArr.push(this.inputData);
                  }
                  this.cacheArr.push(value);
              }
          },

          /** 输入处理 */
          inputHandler (e) {
              let type = e.target.dataset.type;
              let value = e.target.dataset.value;
              let level = e.target.dataset.level;

              //重新计算判断：1. 上次计算后输入 num 或 opr0; 2. 上次计算结果是 NaN；
              if(this.calculated===1 &&
                  (e.target.dataset.type==='num'  ||
                   e.target.dataset.type==='opr0' ||
                   !this.isNumber(this.numStack[this.numStack.length-1]))){

                  this.init();
              }

              //继续计算判断：1. 上次计算后输入 opr1 或 opr2
              if(this.calculated===1 && (type==='opr1' || type==='opr2')){
                  this.cacheArr = [];
                  this.cacheArr.push(this.numStack[this.numStack.length-1]);
                  this.cacheArr.push(value);
              }

              // inputData 从显示模式调整为输入模式
              if(this.inputShow === 1){
                  this.inputData = '';
                  this.inputShow = 0;
              }

              //处理数字-----------------------------------------
              if(type === 'num'){
                  this.inputData += value;
                  this.calculated = 0;
              }

              //处理管理级操作符---------------------------------
              if(type==='opr0'){
                  switch(value){
                      case 'CE':
                          this.inputData = '';
                          break;
                      case 'C':
                          this.init();
                          break;
                      case 'backspace':
                          this.inputData = this.inputData.slice(0, this.inputData.length-1);
                          break;
                      default: break;
                  }

              }

              //处理其他操作符----------------------------------
              if(type==='opr1' || type==='opr2'){
                  //查看寄存器是否有数据
                  if(this.inputData) {
                      this.numStack.push(parseFloat(this.inputData));
                      this.inputData = '';
                  }

                  //容错机制
                  //1. 如果输入二元操作符-等号，则删掉二元操作符
                  //2. 如果连续输入两次二元操作符，则删掉前一个
                  if(this.preType === 'opr2' && value === 'equals'){
                      this.oprStack.pop();
                      this.cacheArr.splice(-2,1);
                  }
                  if(this.preType === 'opr2' && type === 'opr2') {
                      this.oprStack.pop();
                      this.cacheArr.splice(-2,1);
                  }

                  if(value==='equals')
                  {
                      this.oprStack.push({type:type, value:value, level:level});
                      this.preCalculate();
                      this.calculated = 1;
                  }
                  else{
                      this.oprStack.push({type:type, value:value, level:level});
                      this.calculated = 0;
                  }

              }

              //保存等号前的最后一个输入类型--------------------
              if(value!=='equals'){
                  this.preType = type;
              }

          },

          /** 计算预处理 */
          preCalculate () {

              //计算
              this.calculate(this.oprStack, this.numStack);

              //显示结果数据
              if(this.oprStack.length <= 0){
                  this.inputData = this.numStack[this.numStack.length-1];
                  this.errorOccurred = this.isNumber(this.inputData) ? 0 : 1;
                  this.inputShow = 1;
              }

          },

          /** 计算过程 */
          calculate(oprStack, numStack){

              //开始对操作符栈和操作数栈进行迭代计算
              let tmpOpr;
              let tmpNum;

              let item = oprStack[oprStack.length-1];
              let last = oprStack[oprStack.length-2] || '';

              if(last === '' || item.level > last.level){

                  item = oprStack.pop();

                  //计算过程
                  if(item && item.type === 'opr1'){
                      switch(item.value){
                          case 'factorial':
                              numStack.push(this.factorial(numStack.pop()));
                              break;
                          case 'negate':
                              numStack.push(this.negate(numStack.pop()));
                              break;
                          case 'equals':
                              break;
                          default:break;
                      }
                  }
                  else if(item && item.type === 'opr2'){

                      let num1 = numStack.pop();
                      let num2 = numStack.pop();
                      switch(item.value){
                          case 'divide':
                              numStack.push(this.divide(num2, num1));
                              break;
                          case 'multiply':
                              numStack.push(this.multiply(num2, num1));
                              break;
                          case 'minus':
                              numStack.push(this.minus(num2, num1));
                              break;
                          case 'plus':
                              numStack.push(this.plus(num2, num1));
                              break;
                          default:break;

                      }
                  }

                  //迭代
                  if (oprStack.length > 0){
                      this.calculate(oprStack, numStack)
                  }


              }
              else{ //分治
                  tmpNum = this.numStack.pop();
                  tmpOpr = this.oprStack.pop();
                  this.calculate(this.oprStack, this.numStack);
                  this.oprStack.push(tmpOpr);
                  this.numStack.push(tmpNum);
                  this.calculate(this.oprStack, this.numStack);

              }

          },



          /** 具体计算函数 */
          factorial (a) {
              let result = 1;
              while(a >= 1){
                  result *= a;
              }
              return result;
          },
          negate (a) {
              return 0-a;
          },
          divide (a, b) {
              return a / b;
          },
          multiply (a, b) {
              return a * b;
          },
          minus (a, b) {
              return a - b;
          },
          plus (a, b) {
              return a + b;
          },

          /** 判断空对象 */
          isEmptyObject(obj){
              let t;
              for(t in obj){
                  return !1;
              }
              return !0;
          },

          /** 判断数字 */
          isNumber(value){
              return typeof value === 'number' && isFinite(value);
          }
      }

  }
</script>

<style scoped>

  #panel, #board{
    width: 100%;
    text-align: center;
  }
  #panel div{
    height:auto;
    min-height: 22px;
    text-align: right;
    word-wrap: break-word;
    padding: 0 5px;
  }
  #board table{
    width: 100%;
  }
  #board table td{
    width: 25%;
    height: 40px;
  }
  #board table td:hover{
    background-color: darkgrey;
  }

  .text-danger{
    color: #f00;
  }
</style>



### 简介
这是一个计算器组件。

包括四则运算和单位转换（可配置是否包括单位转换）

- 四则运算部分支持单次运算、连续计算、（从上次结果）继续计算，包括一定的容错机制。（计算模式配置将在未来版本支持）
- 单位转换部分支持体积、重量、长度、温度、角度的转换。

特点：轻量、灵活；可定制；可扩展

使用要求：Vue 框架

### 基本用法

1. 在将组件文件 Calculator.vue、CalNum.vue、CalUnit.nue 加入 src 文件夹。
2. 在你要使用本组件的页面中加入如下代码：
```htmlbars
<template>
<div id='app'>
    <calculator 
      :cal-num-options = {'science': false, 'programmer': false} 
      :cal-unit-options = true > 
    </calculator>
</div>
</template>

```

```javascript
<script> 
// 引入
import Calculator from 'calculator'

export default{
    //注册
    components: {Calculator}
}
</script>
```
3. 运行步骤：
 - `npm install` 安装依赖
 - `npm run dev`  服务器运行在 localhost: 8080 并设置热加载
 - （按需）打包、编译、构建成最终项目文件`npm run build`  


### 属性

| 参数 | 说明 | 类型 | 可选值 | 默认值 |
| :-------- | :--------| :------ | :------ | :------ |
| cal-num-options(待实现) |   计算器模式配置 |  object | -- | {'science': false, 'programmer': false}|
| cal-unit-options |   是否开启单位转换 |  boolean | -- | false|

### cal num options(待实现)

| 参数 | 说明 | 类型 | 可选值 | 默认值 |
| :-------- | :--------| :------ | :------ | :------ |
| science |  科学模式 |  boolean| --| false|
| programmer | 程序员模式 |  boolean| -- | false|

### 更新日志

v0.1 计算器标准模式 + 文档
v0.2 增加转换器 

### 版本规划
v0.3 计算器增加科学模式、程序员模式
v0.4 事件接口完善

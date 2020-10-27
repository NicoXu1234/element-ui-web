## BUG 源码修改

***
# 1, element-ui2.4.8已经修复
https://blog.csdn.net/zjw0742/article/details/77746047

~~element ui 表格动态列显示空白bug 修复~~
~~在使用element ui框架进行项目开发的时候，表格的列是根据后台数据动态生成的，但是发现在列刷新的视乎，会出现表格完全空白，没有显示的情况，经过自己编写demo发现，在增加列的情况下表格正常，但是一旦表格列减少时就会出问题，对element底层代码进行调试发现，在node_modules/element-ui/lib/elementui.common.js 中的一个函数~~

```
removeColumn: function removeColumn(states, column) {
  var _columns = states._columns;
  if (_columns) {
    _columns.splice(_columns.indexOf(column), 1);
  }
```

这个函数中_columns是一个数组，column是一个对象，当indexOf匹配不到的时候，返回-1，但是splice函数会执行删除操作，所以必须加入判断
```
removeColumn: function removeColumn(states, column) {
  var _columns = states._columns;
  if (_columns.indexOf(column) != -1) {
    _columns.splice(_columns.indexOf(column), 1);
  }
```
动态列问题解决
element-ui 2.4.8 fixed this bug
***
# 2
修复ie11下，date-picker组件无法从store获值显示（实际上是有值，input却是空的)，修改如下
如下路径node_modules/element-ui/lib/elementui.common.js
搜索handleClickIcon方法，在showClose == true 逻辑最下方添加如下
if(this.refInput.length > 0 ){
  this.handleFieldReset(); 
}

***
# 3
修复element-ui2.4.8 computeds属性计算form-item取值失效的问题
如下路径node_modules/element-ui/lib/elementui.common.js
搜索fieldValue方法，修改为

        ,
        fieldValue: {
            cache: false,
            get: function () {
                ******
            }
        }
        ,

***
# 4
修复element-ui 修复时间组件打开picker-panel只选择开始时间后退出选择，下次打开picker-panel后只能选择结束时间  
如下路径node_modules/element-ui/lib/elementui.common.js  
搜索resetView方法（有多个，均需要修改）：  
```
resetView: function resetView() {
  // NOTE: this is a hack to reset {min, max}Date on picker open.
  // TODO: correct way of doing so is to refactor {min, max}Date to be dependent on value and internal selection state
  //       an alternative would be resetView whenever picker becomes visible, should also investigate date-panel's resetView
  this.minDate = this.value && isDate(this.value[0]) ? new Date(this.value[0]) : null;
  this.maxDate = this.value && isDate(this.value[0]) ? new Date(this.value[1]) : null;
}
```
在方法内部最下方添加如下 
```
this.rangeState.selecting = false;
```
***

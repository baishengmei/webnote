### 新智选数据报表／app行为定向／动态词包开发总结
##### *说明：分功能块介绍*

#### 1. localStorage服务端渲染undefined问题
+ 服务端组件的生命周期


> 和客户端并不完全相同，componentDidMount和componentWillUnmount不会在服务端渲染过程中被调用，componentWillMount和render，会在node端进行渲染。

+ 解决办法

> typeof localStorage !== 'undefined' && (localStorage.getItem('appOrient') !== 'instruction') ? 'down' : 'right')

#### 2. [moment()](http://momentjs.cn/)库，服务端渲染后回日期格式被序列化
+ 服务端序列化后的格式：`"2017-11-03T02:50:26.421Z"`
+ 时间组件根据该值重置日期时，需要为moment对象格式，此时需要在渲染后，将序列化后的日期重新moment格式处理

```
((state) => {
  const queryCondition = state.toolbox.formComponent.list.queryCondition;
  const today = moment();
  queryCondition.dateRange = {
    startDate: moment().subtract(180, 'days'),
    endDate: today,
  };
})(window.APP_STATE);
```
#### 3. 有关搜索输入框的一个功能
+ 功能描述

```
输入框输入关键词，得到搜索列表，选中某项；
输入框内填入了某项，显示北京颜色为浅红色，而且该输入框根据填写内容的长度自动换行，背景颜色根据内容进行显示；
```
+ 实现方法

```
<div>//相对定位
	<Select> //选择框
	</Select>
	<div> // 绝对定位，设置背景颜色
		<div> // 根据选择框的尺寸，调整margin，这里写了200个div！！！～！！
		</div>
		……
		<div>
		</div>
	</div>
</div>
```
+ 同理：该输入框内的“竖线”和“查询”按钮也利用该定位实现；

#### 4. input框，输入标题
+ 问题描述

```
1. 可在input框任意光标选中位置插入“词包”;
2. 并且光标放到该词包内，可一次性删除整个词包;
3. 不可手动输入类词包内容
4. 键盘左右键／del键删除，也可一次性删除词包
5. **如果选中某几个词，点击了插入，则会将该选中的词替换为插入内容**
```
+ 实现思路


> 1. 可以利用e.target.selectionStart获取光标位置；
> 
> 2. 获取用户选择区域，首先搜到的解决办法是**（1）document.selection.createRange().text，（2）window.getSelection().anchorOffset和window.getSelection().focusOffset**，但是为了避免在react中操作document，没有找到更好的办法来使用selection.createRange().text；
> 3. 后来发现，当为选中状态时，可以使用**e.target.selectionStart和e.target.selectionEnd**获取选中区域的首位位置；
> 4. 该input用到的事件**onClick／onKeyDown／onFocus／onChange／onMouseOver／onMouseLeave／onBlur**
> 5. 其中onClick，可以获取到光标位置；
> 6. onKeyDown可以监听键盘是否按下了del键，以及左右键；这里还会有另一个问题，左右键移动时，无法监听到光标位置，于是这里就需要自己计算；
> 7. onFocus控制是否显示“插入”按钮；
> 8. onChange时，禁止输入类词包内容{地点},禁止输入{和}；
> 9. onMouseOver和onMouseLeave判断是否显示‘插入’按钮；
> 
> 10. **遇到的另一个问题是onBlur～～～～～～**
>     ～当点击“插入”按钮“时，会发现不出发，该字段的onClick事件，而是触发input的onBlur事件。但是onBlur又是必须的。这里的解决办法是：用了一个setTimeOut，可以解决该问题；
> 11. 需要注意的是，如果这里用了setTimeOut，在事件onMouseOver／onFocus／onMouseLeave时，要执行clearTimeout才可以，否则会导致行为和功能时间错位的问题
> 
> 

#### 5. mock-server的开发进度，及问题讨论
+ mockserver旨在给前后端开发人员定义接口、测试接口用的一个系统。
+ 前后端开发人员在评审期，在mockserver系统完成请求与响应的规则定义；
+ 前端开发人员，可以发送请求到mockserver系统，该系统会校验收到的request是否符合规则，若符合，则按照响应规则生成响应，并返回给前端；
+ 后端开发人员，接口完成后，将实际url配置到mockserver系统，并点击‘发送请求’按钮，该系统会将对应的请求规则生成请求，发送到该url对应的后端接口，如果请求成功则返回后端数据，mockserver系统判断返回的响应是否满足响应规则，若满足，则将返回结果显示到界面里。反之，以上过程，任一不满足的步骤均会报出响应错误到mockserver系统界面中。
+ 目前已经完成了规则定义及数据库保存；接收请求、判断请求、生成响应；配置url、发送请求到后端、校验后端返回信息是否正确；以上就是目前完成的简单demo；
+ 由于最初写代码时，没有恰当、合理的做出需求设计，导致写代码过程中，某些用例需求校验冲突和丢落。目前仍存在很多的问题亟待解决。
+ 如果对象可能包括数组，数组包括对象，还可能包括整数类型。这种数据该如何存储呢？目前不支持对象自定义类型的无限嵌套。
+ 需求设计欠缺导致demo代码不完善，适用的场景比较少；




![WX20231215-030411](/Users/canonls/Desktop/WX20231215-030411.png)

```javascript
//component组件文件
store.dispatch(createIncrementAction(value * 1));//下发分发action
//action.js //创造处理好action //提供给组件使用
export const createIncrementAction = data => ({ type: INCREMENT, data });
export const createDecrementAction = data => ({ type: DECREMENT, data });

//constant.js 
//约束，交给 action和countReducer 
//相当于action和countReducer的中间人，协定了一些规则
export const INCREMENT = "increment";
export const DECREMENT = "decrement";

//reducer.js文件
function countReducer(preState = 0, action){
// 处理/加工action的方法/交给store
  const { data, type } = action;
	switch (type) {
		case INCREMENT:
			return preState + data;
		case DECREMENT:
			return preState - data;
		default:
			return preState;
	}
}
//store.js
 createStore(countReducer) //创造了一个包含处理方式的store并且暴露给需要的组件



```


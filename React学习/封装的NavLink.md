```react
export default class MyNavLink extends Component{
	render(){
		return(
			<NavLink activeClassName="active" className="xxx"{...this.props}/>
		)
	}
}
//二次封装NavLink插件
// 组件会接受到调用时候的标签体内容 字段key为children
//this.props{
//		to:"/about",
//		children:"title"
//}
//调用⬇️
<MyNavLink to="/about">title</MyNavLink>
```


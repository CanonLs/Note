![image-20231211035539571](/Users/canonls/Library/Application Support/typora-user-images/image-20231211035539571.png)

```css
.loadPage {
	width: 630px;
	height: 60px;
	position: absolute;
	top: 50%;
	left: 50%;
	transform: translate(-50%, -50%);
	border-radius: 35px;
	border: 5px solid #fff29d;
	overflow: hidden;
	.loadPre {
		width: 20%;
		height: 100%;
		background: #fff29d;
		border-radius: 35px;
		border: 0px;
		position: absolute;
		top: 50%;
		transform: translateY(-50%);
	}
}
```

正常排版会出现缝隙 需要将外层的border写在伪类上

```css
.loadPage {
	width: 630px;
	height: 60px;
	position: absolute;
	top: 50%;
	left: 50%;
	transform: translate(-50%, -50%);
	overflow: hidden;
	.loadPre {
		width: 20%;
		height: 100%;
		background: #fff29d;
		border-radius: 35px;
	}
}

.loadPage::before {
	border-radius: 35px;
	content: "";
	position: absolute;
	left: 0;
	top: 0;
	right: 0;
	bottom: 0;
	border: 5px solid #fff29d;
	pointer-events: none;
}
```


Video.js 加载视频失败切换其它 source
使用 Video.js 加载视频（本例为 m3u8 直播视频源）时，如果失败就会显示错误提示 UI。
一些场景下无法进行手动刷新，所以需要对加载失败的场景进行处理，尝试加载其它可用的视频源。
本例介绍 Video.js 如何切换 source、如何捕获错误，以及一些细节。
Video.js 提供的 API 有多种方式可以实现这个功能，本例只是其中一个方案。

```
<!DOCTYPE html>
<html lang=“en”>
<head>
<meta charset="UTF-8” />
<title>Video.js 加载失败切换其它视频源</title>
<link href="vjs.zencdn.net rel="stylesheet” />
<script src="vjs.zencdn.net
</head>
<body>
<video id="my-video" class="video-js"></video>
<script>
// 测试用的视频源
const sources = [
{
src: 'aaa
type: 'application/x-mpegURL’
},
{
src: 'bbb
type: 'application/x-mpegURL’
},
// 第三个真实可用
{
src: 'live.unified-streaming.com
type: 'application/x-mpegURL’
}
]
let index = 0 // 当前加载的视频源序号
var player = videojs('my-video', {
width: 500,
controls: true,
preload: ‘auto’,
autoplay: 'muted', // 实现视频自动播放的关键
sources: sources[index]
})
videojs.hook('beforeerror', (player, err) => {
console.log('beforeerror', index, player.src(), err)
// Video.js 在切换/指定 source 后立即会触发一个 err=null 的错误，这里过滤一下
if (err !== null) {
player.src(sources[++index])
}
// 清除错误，避免 error 事件在控制台抛出错误
return null
})
// 其它可以观察进度的事件和钩子
// videojs.hook('error', (player, err) => {
// console.log(‘error’)
// return err
// })
// player.on('loadeddata', () => {
// console.log(‘loadeddata’)
// })
// player.on('loadedmetadata', () => {
// console.log(‘loadedmetadata’)
// })
// player.on('loadstart', () => {
// console.log(‘loadstart’)
// })
</script>
</body>
</html>
```

细节说明
自动播放
以前可以通过给 video 元素或 Video.js 选项设置 autoplay 自动播放（非静音的），但是后来 Chrome 做出了限制，禁止音频自动播放。
这就导致当 Video.js 自动或手动调用 player.play() 执行播放时，Chrome 控制台会报错：DOMException: play() failed because the user didn't interact with the document first.
Chrome 的目的是避免音频的自动播放骚扰到用户的意外情况，但也划定了允许视频自动播放的条件，例如设置静音的视频允许自动播放。
你可以在 video 元素上设置 muted 和 autoplay 属性，也可以在调用 Video.js 的 play() 方法前先设置静音，例如：
```
// 当前视频加载到足够持续播放的数据后触发 loadeddata
// 也可以使用 loadedmetadata 事件，它在加载完视频元数据后触发，也足够播放视频了
player.on('loadeddata', () => {
// 先设置静音
player.muted(true)
// 再执行播放
player.play()
})
```

又或者设置 autoplay 选项为 muted，它的效果相当于上面的代码。
虽然官方说 autoplay: 'muted' 选项 会在 loadstart 时自动执行 player() 方法，但是 loadstart 事件只是完成开始加载事件时触发，执行事件处理函数时，并不能保证加载的视频数据片段足够进行播放甚至可能加载失败，所以在 loadstart 事件中手动执行 player() 依然会报错，可是通过设置 autoplay: 'muted' 选项并未报错，大概是调用时机比文档中说的要靠后些。（简单看了下源码不太好找就放弃了）
关于 Chrome 禁止自动播放声音请查看：Chrome 66禁止声音自动播放之后
错误捕获
示例代码中用了两个钩子 beforeerror 和 error，它们都是当捕获到错误时触发。
beforererror
beforererror 用于捕获到错误，并在浏览器控制台抛出错误前触发。
它必须返回一个错误信息或代表错误已清除的 null。
如果它返回有效的错误信息（不是 null），则会触发 error 钩子，并作为参数传递过去。
如果返回 null，就不会触发 error 钩子。
默认情况下它就是把错误直接 return。
error
error 用于捕获 beforerror 返回的错误信息，默认情况下，它会直接将错误展示在浏览器的控制台。
使用选择
如果不想在控制台抛出错误信息，可以使用 beforeerror，处理业务逻辑后最终返回 null 清理错误。
但 beforeerror 在切换/指定 source 后立即会触发一个 err=null 的错误，所以内部还需要额外判断一下触发时是否真的是错误导致。
如果不想进行额外的判断，且无所谓控制台是否显示错误信息，则可以直接使用 error，简单省事。
请注意：用于钩子处理函数是全局添加的，在实际开发中请记得在必要时机移除（removeHook）钩子处理函数，在存在多个不同处理的视频播放的页面中可能还需要定义判断播放器的逻辑。
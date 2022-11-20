
# 缓冲范围

获取音频的缓冲范围可以使用 `audio.buffered`，次属性保存的是 `TimeRanges` 类型的数据，类似于数组，需要使用下标获取，且在控制台是无法查看的

在 audio 每次缓冲会触发 `progress` 事件，可以在此事件中对缓冲范围进行操作

使用 `audio.buffered` 配合监听 `progress` 事件可以实时更新缓冲范围
```
audio.addEventListener('progress', () => {
    for (let i = 0; i < this.video.buffered.length; i++) {
        console.log('range start', this.video.buffered.start(i),'range end', this.video.buffered.end(i), i)
    }
});
```

之前提到 `audio.buffered` 获取到的 `TimeRanges` 对象类似数组，实际获取到的 `TimeRanges` 可能不是连续的，比如当前缓冲范围是 0-30，而用户对音频的进度进行快进，跳转到 60，那么再次获取 audio.buffered 得到 TimeRanges 的 `length` 长度会改变，增加 1，此时获取下标为 1 的数据，范围可能是 55-80

# audio.buffered获取报错

```
Uncaught DOMException: Failed to execute 'end' on 'TimeRanges': The index provided (0) is greater than or equal to the maximum bound (0).
```

这是因为获取 buffered 时，length还是 0 导致的，可以通过使用前`判断 buffered 的 length` 是否大于 0 来避免此错误


# ios端safari浏览器播放audio

在IOS开发文档中有这样一句
```
note：The preload attribute is supported in Safari 5.0 and later. Safari on iOS never preloads.
```

意思是：`在Safari 5.0及更高版本中支持preload属性。iOS上的Safari从不预加载`

所以在 ios 中不会自动加载音视频，所以 canplay 事件不会自动执行，在 pc 端和 android 端，系统会自动加载音视频，所以 canplay 会自动被执行，这导致 ios 的 canplay 事件是在 play() 之后再执行的

这会导致在 canplay 等事件中等待执行的播放前准备代码无法执行，比如获取音频的播放时长

此时我们可以不使用 canplay 事件来监听，改成使用 `durationchange` 事件来监听当前音视频时长是否发生变化

safari 不能自动播放音频，需要用户点击之后才能播放，我解决方式是在 emptied 事件中调用 play() 直接播放，当 audio 制空自然无法播放，而当调用 audio.load() 的时候，emptied 也会执行，也就自动播放了

在 ios 手机端 safari 浏览器无法调整歌曲进度，每当跳转进度时会导致歌曲播放暂停，如果监听了 ended 事件会直接触发 ended 事件，即使歌曲已经加载完成

我在使用时有碰到过这个问题，是获取哔哩哔哩音频然后返回到自己的页面上时出现，但当使用网易云音乐的音频 url 直接赋值给 audio 标签时是可以正常跳转进度的

关于这点只找到一个很久之前的文章有提到：[关于有些MP3不能在safari上快进的问题](https://blog.csdn.net/suxianbaozi/article/details/9184613)，内容如下
```
mp3 的编码格式有三种
ABR CBR VBR
啥东西不细说，但是有点区别

VBR是最完美的编码格式  编码速率 动态变化
CBR是比较老的格式  编码速率 不变 
ABR前两者之间   
但是一些老的播放器对VBR的支持不是很好

关键点来啦
VBR在safari的audio播放器里不能快进，对audio设置 currentTime直接歌曲播放结束，蛋疼吧，哈哈哈哈哈

在用ffmpeg或者avconv 进行其他格式转换到MP3的时候，默认用的是VBR编码，如何制定为CBR呢，方法如下

avconv -i 原文件 -ab 128k -f mp3 目标文件  

最大速率和最小速率相等，就自动用cbr编码啦，嘿嘿嘿
```

# 事件

canplay 事件在 pc 端 chrome 和安卓端是会触发多次的，但在 ios 的 safari 只会在 play() 调用后触发一次

audio 加载错误信息需要获取 audio 标签，使用 dom.error 获取错误信息，错误信息为 MediaError 类型，具体字段查看[mdn链接](https://developer.mozilla.org/en-US/docs/Web/API/MediaError)


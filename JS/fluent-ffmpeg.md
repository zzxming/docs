# 流式输出转换后的数据

举例将某 MP3 音频调整比特率后输出
```
let command = new ffmpeg(file)
    .audioBitrate('128k')
    .format('mp3')
    .pipe('123.mp3')
```

使用 `.pipe` 方法,但在使用时一定要使用 `.format` 规定文件类型,否则会有下方报错信息

```
ffmpeg exited with code 1: pipe:1: Invalid argument
```

使用流式数据时要在 pipe 方法第二参数传入对象 `{ end: true }`

```
let command = new ffmpeg(fs.createReadStream(filePath))
    .audioBitrate('128k')
    .audioCodec('libmp3lame')
    .format('mp3')
    .on('start', a => console.log(a))
    .pipe(res, { end: true })
```


# progress percent returns undefined

在监听 `progress` 事件时,可以收到带有属性 `percent` 的对象,但某些时候对象中不包含此属性,如果任然想得知进度的话,可以通过文件的 duration 和转换至的当前时间进行计算.

这个方法当转换的当前时间是负数时会报错
```
let command = new ffmpeg('123.mp3')
    .on('stderr', function(line) {
        var res = inputDir.split(" ")

        if (line.trim().startsWith('Duration')) {
            var match = line.trim().match(/Duration:\s\d\d\:\d\d:\d\d/).toString().split('Duration:').slice(1).toString().split(':');
            duration = +match[0] * 60 * 60 + +match[1] * 60 + +match[2];
        } else if (line.trim().startsWith('size=')) {
            match = line.trim().match(/time=\d\d\:\d\d:\d\d/).toString().split('time=').slice(1).toString().split(':');
            var seconds = +match[0] * 60 * 60 + +match[1] * 60 + +match[2];
            percent = seconds / duration;
            percent = (percent * 100).toFixed()
            $('#percent').html(percent + '%')
            $('.determinate').css('width', percent + '%')
            console.log(`stderr: ${line}`)
            console.log(`${percent}%`)
        }
    })
```


## **video/audio标签的src**
建议src地址不要为空,如果刚开始为空的话可能会出现不进行网络请求歌曲地址的情况

浏览器只有在获取到视频的元信息（metadata）后才开始播放视频，而一般不经过优化的视频，其元数据信息存在视频文件末尾

## **video标签出现多个http请求**

请求视频这类文件的时候因为响应会很大，如果全部下载下来再播放会等待很长时间，用户体验不好，所以服务器会允许浏览器使用range头来请求视频的某一部分，并设置以206的响应码进行响应，来使浏览器可以边播放边请求，优化用户体验。所以会出现状态码为206的响应。

另一方面，浏览器只有在获取到视频的元信息（metadata）后才开始播放视频，而一般不经过优化的视频，其元数据信息存在视频文件末尾。所以会出现连续三个请求的情况：第一个请求是先从0字节找视频的元数据信息，没找到的话，会继续发起一个请求到视频末尾去找，等找到以后，又会重新开始一个请求从视频开头处开始请求，此时视频才开始播放。如果视频经过优化，元数据信息在视频开头，就只会有一个请求了，所以推荐使用HandBrake这类的工具对视频经过网络优化后再上传。

详见：https://www.zhangxinxu.com/wordpress/2018/12/video-moov-box/

一个MP4视频文件，不单单是视频内容，还有很多其他信息，尺寸，时长，字幕，版权信息等。这些信息被放在一个一个的 box 中，换句话说就是，一个 MP4 文件由很多个 box 组成的，用以存储媒体信息。其中，有个与请求数有关的box名叫MOOV BOX。

Moov box 存放的是如何播放视频的信息，如尺寸和每秒的帧数，则存储在叫做 moov 的特殊 box 中。你可以认为 moov box 是某种意义上的 MP4 文件目录。

当你播放视频时，程序会查找 MP4 文件，定位 moov box 的位置，然后借此去查找视频和音频的起始位置来开始播放。Box 可能以任意顺序排列，所以程序一开始并不知道 moov box 哪里。如果是本地播放，没有任何问题，因为你已经拥有整个视频文件；但如果在线观看，也就是流传输 HTML5 视频时，就会有问题了。

浏览器直接发起 HTTP MP4 请求，读取响应 body 的开头，如果发现 moov 在开头，就接着往下读 mdat(media data container)。如果发现开头没有，先读到 mdat，立马 RESET 这个连接，节省流量，通过 Range 头读取文件末尾数据，因为前面一个 HTTP 请求已经获取到了 Content-Length ，知道了 MP4 文件的整个大小，通过 Range 头读取部分文件尾部数据也是可以读取到的。


## **nodejs/express流式加载**
```
const filePath = path
const stat = fs.statSync(filePath);
const fileSize = stat.size;
const range = req.headers.range;
if (range) {
    //有range头才使用206状态码
    const parts = range.replace(/bytes=/, '').split('-');
    const start = parseInt(parts[0], 10);
    // end 在最后取值为 fileSize - 1
    const end = start + 3000000 > fileSize ? fileSize - 1 : start + 3000000;
    const chunksize = (end - start) + 1;
    const file = fs.createReadStream(filePath, { start, end });
    const head = {
        'Content-Range': `bytes ${start}-${end}/${fileSize}`,
        'Accept-Ranges': 'bytes',
        'Content-Length': chunksize,
        'Content-Type': 'audio/mp4',
    };
    res.writeHead(206, head);
    file.pipe(res);
} else {
    const head = {
        'Content-Length': fileSize,
        'Content-Range': `bytes 0-3000000/${fileSize}`,
        'Content-Type': 'audio/mp4',
    };
    res.writeHead(200, head);
    fs.createReadStream(filePath).pipe(res);
}

```

## **无法调整进度**

音频可以播放，但是调整进度就回到 0 秒，重新开始播放

加上 Accept-Ranges: bytes 响应头和 http 响应码 206
```
res.set({
    "Content-Type": "video/mp4;charset=UTF-8",
    "Accept-Ranges": "bytes",
})
```

如果不设置 206 状态码，audio/video 不会自动请求下一段的数据

这个有关断点续传，因为不是从静态文件输出，而是动态输出流媒体文件。断点续传允许客户端从服务器提取某个文件指定字节范围内的一部分内容，当下载中断以后再次下载时可以只请求下载原先没有下载的部分，避免重复传输现有内容。当客户端（浏览器）检测到服务器支持断点续传以后才会发送相应的区间内容请求给服务器，服务器接到请求以后再返回相应范围内的文件内容，这样才能实现流媒体文件的定点播放。而直接使用静态文件做播放资源时，服务器软件通常会自动处理断点续传请求。

发送 Accept-Ranges: bytes 和 Content-Range: bytes S-E/FILESIZE 两个响应头，告诉客户端它请求的服务器资源支持断点续传，服务器端接收到客户端发送的请求以后，从请求头拿到需要发送的内容区间，然后从文件中读取指定起止位置的数据发送到客户端即可解决 HTML5 audio/video 使用后端语言动态输出媒体文件内容造成的 currentTime 失效以及无法手动拖动进度条的问题。其中S和E分别是当次请求的起始位置索引和结束位置索引，FILESIZE 为代表文件尺寸的总字节数。

关于流式传输，需要设置 content-range 范围，如果只设置头，则自动请求完全部

需要设置响应头 206，否则不会自动请求下一段数据

nodejs 里获取其他网址的数据，如果获得的是 readablestream 则可以通过请求到的 data 直接 .pipe(res) 发送出去，如果要流式的话得设置响应头和响应 http 状态码 206

设置的 content-range 范围的最大值，不能超出 content-length，否则会导致音频不能播放


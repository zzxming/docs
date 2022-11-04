## **网易云音乐**

nodejs 有网易云音乐的包

https://binaryify.github.io/NeteaseCloudMusicApi/#/?id=neteasecloudmusicapi

## **网易云音乐外挂插件**

官方使用教程

https://music.163.com/#/outchain/0/5388442370

插件中的 iframe 的 src 参数
```
type: 0 为歌单, 2 为歌曲
id: 歌曲或者歌单的id
auto: 是否自动播放
```

歌单只会获取前十首


## **获取网易云音乐**

根据网易云音乐的 id 返回音频数据，只要是能完整播放的就可以获取，如果是 vip 试听则返回的是 404 页面

```https://music.163.com/song/media/outer/url?id=音乐id.mp3```


## **b站iframe**

```https://player.bilibili.com/player.html```

路径 url 参数
```
bvid: bv号
page: 视频分p, 默认1
as_wide: 是否宽屏, 1宽屏, 0小屏
high_quality: 清晰度, 0是360p, 1是720p
danmaku: 是否开启弹幕, 1开启, 0关闭, 默认1
```

如果发现页面中没有视频控件，原因可能是iframe太小，设置高度和宽度到一定值会显示控件


## **b站搜索**

```https://api.bilibili.com/x/frontend/finger/spi```

通过此接口获取 cookie 需要的参数值，b_3，cookie 的 key 值为buvid3

```http://api.bilibili.com/x/web-interface/search/all/v2```

通过此接口获取搜索结果
路径 url 参数

```
page: 当前页数
page_size: 一页的数据量
keyword: 关键词
```

## **b站视频或音频**

```https://api.bilibili.com/x/player/pagelist?bvid=bvid```

此 url 可通过 bv 号获取 cid

```https://www.bilibili.com/video/bvid```

从html中获取 `__INITIAL_STATE__` ，从此变量中获取 bvid、cid 如果是特殊页面(如拜年祭的播放页面)，可从其中获取变量 videInfo，再从 videoInfo 中获取 bvid、cid。

```https://api.bilibili.com/x/player/playurl?cid=cid&bvid=bvid&fnval=4048```

此 url 可获取 playinfo。fnval 参数必须要传，否则返回的 url 无效，是 .flv 格式

从 playinfo 中，可以得到 audio、video 的获取路径，之后通过获取路径，axios 直接请求路径可获得对应文件。axios 设置 responseType: "stream"，可得到数据流，通过 nodejs 中 fs.createWriteStream 创建写入流，通过返回变量调用 pipe(writeStream)，传人写入流，可保存至本地。


## **随机图片API**

此方式能返回图片的路径

文档地址：http://api.btstu.cn

请求方式：```get```

请求地址：```https://api.btstu.cn/sjbz/api.php```

返回格式：```json/images```

请求示例：```https://api.btstu.cn/sjbz/api.php?lx=dongman&format=images```



请求参数：
|名称|必填|类型|说明|
| ---- | ---- | ---- | ---- |
| method | 否 | string | 输出壁纸端 [ mobile/pc/zsy ] 默认为pc |
| lx | 否 | string | 选择输出分类 [meizi/dongman/fengjing/suiji] ，为空随机输出 |
| format | 否 | string | 输出壁纸格式 [json/images] 默认为images |


返回参数：
|名称|类型|说明|
| ---- | ---- | ---- |
| code | string | 返回的状态码 |
| imgurl | string | 返回图片地址 |
| width | string | 返回图片宽度 |
| height | string | 返回图片高度 |


返回示例：
```
{S
    "code":"200",
    "imgurl":"https://tva4.sinaimg.cn/large/9bd9b167gy1g2qkr95hylj21hc0u01kx.jpg",
    "width":"1920",
    "height":"1080"
}
```


## **TenApi**

https://tenapi.cn	

免费图片API，进入路径直接返回图片数据，而非图片路径
```
https://api.ixiaowai.cn/api/api.php（二次元）
https://api.mtyqx.cn/tapi/random.php （二次元）
https://api.ixiaowai.cn/mcapi/mcapi.php（mc酱动漫）
https://api.ixiaowai.cn/gqapi/gqapi.php（风景）
https://cdn.seovx.com/?mom=302（真人(PC)）
https://api.btstu.cn/sjbz/api.php（真人(PC)）
https://api.isoyu.com/mm_images.php（手机(小尺寸图)）
```
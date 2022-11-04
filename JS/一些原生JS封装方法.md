
## **获取图片中最多的颜色**
```
let bgcolor = '';
let canvas = document.createElement('canvas');
let oCan = canvas.getContext('2d');
let oImg= new Image();

let colorArr = [];
let maxColor = null;
let index = 0;
oImg.crossOrigin = 'anonymous'
oImg.onload = function(){
    oCan.drawImage(oImg,0,0);
    let data = oCan.getImageData(0, 0,oImg.width,oImg.height).data;//读取整张图片的像素。每四位是一像素的颜色rgba
    for (let i = 0; i < data.length; i += 4) {
        colorArr.push([data[i], data[i+1], data[i+2], data[i+3]]);
    }
    for (let i = 0; i < colorArr.length - 1; i++) {
        colorArr[i].num = 0;
        for (let j = i + 1; j < colorArr.length; j++) {
            if (colorArr[i][0] === colorArr[j][0] && 
                colorArr[i][1] === colorArr[j][1] &&
                colorArr[i][2] === colorArr[j][2] &&
                colorArr[i][3] === colorArr[j][3]) {
                colorArr.splice(j, 1)
                colorArr[i].num++;                   
                j --;
            }
        }
    }
    maxColor = colorArr[0].num;		// maxColor 最多颜色的次数
    for (let i = 1; i < colorArr.length; i++) {
        if (maxColor < colorArr[i - 1].num) {
            maxColor = colorArr[i - 1].num
            index = i - 1;
        }
    }
    bgcolor = `rgba(${colorArr[index][0]}, ${colorArr[index][1]}, ${colorArr[index][2]}, ${colorArr[index][3]})`;
};
oImg.src = ''; //图片地址

```

## **jsonp**
```
// obj = {
// 	url: '',	// 请求路径
// 	data: {}, 	// get的参数
// 	jsonp: 'jsonp', 	// 使用jsonp
//	jsonpCallback: 'callback'+Math.round(new Date().getTime()*Math.random()),	// jsonp回调函数,保证函数名在window上不重复
// 	success:function	// 当window上没有jsonpCallback时,执行
// }
const _jsonp = (obj) => {
	const script = document.createElement('script');
	let dataStr = '';
	let callbackName = obj.jsonpCallback ? 
		obj.jsonpCallback : 
        //如果出现两个同时间戳的同名函数会报错,保证时间戳是整数,没有小数点
		'jsonpCallback_' + Math.round(new Date().getTime()*Math.random());
	if (typeof obj.data === 'object') {
		for(let attr in obj.data){
			dataStr += `${attr}=${obj.data[attr]}&`;
		}
	} else if (typeof obj.data === 'string') {
		dataStr += obj.data
	}
	window[callbackName] ? '' : window[callbackName] = function (res) {
		obj.success(res);
		delete window[obj.jsonpCallback]		// 删除在全局内的jsonp回调函数
	}
	script.src = `${obj.url}?${obj.jsonp}=${callbackName}&${dataStr}`;
	document.body.appendChild(script);
	document.body.removeChild(script);
}
```
需要后端配合返回

后端接收到参数 { jsonp: 'callback' }

jsonp是前端所执行函数名,所以需要在后台返回数据的时候用字符串包裹调用，最后返回函数调用的形式
```
// "callback({code:1,message'成功'})"
res.send(`${req.query.jsonp}({code: 1, message: '成功'})`)
```
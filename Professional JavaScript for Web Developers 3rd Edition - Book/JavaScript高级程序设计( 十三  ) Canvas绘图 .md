学习一下Canvas然后开始试着写个放置类的游戏吧。

# 基本用法 #
要使用canvas进行绘图需要在HTML中设置

    <canvas id="drawing" width="1000" height="500"> you borwser not sup canvas </canvas>

支持获取引用修改宽高， 默认无样式 不再页面之中显示，

要想绘图先需要引入绘图上下文（可以认为是绘图API），

	var draw = document.getElementById("drawing");

    if(draw.getContext){ //检测浏览器是否支持
    var content = draw.getContext("2d");
    }

# 2D上下文 #
使用2d上下文可以绘制2D图形

## 填充和描边 ##
2D上下文的两种基本操作，填充和描边。填充就是用样式填充图形，描边就是在图形的边缘画线。
这两者操作的结果取决于两个属性 fillStyle和strokeStyle



## 绘制矩形 ##
矩形是唯一可以直接绘制出来的形状hui

绘制矩形一般有三种方法 分别对应填充矩形 边框矩形 清除矩形块区域



	var draw = document.getElementById("drawing");

	if(draw.getContext){
    var content = draw.getContext("2d");

    content.fillStyle = "#ff0000";
    content. fillRect(10,10,500,500); //fillRect绘制的矩形会自动设置为fillstyle的属性

    content.fillStyle = "rgba(0,0,255,0.3)";
    content.fillRect(400,400,100,100);

    content.strokeStyle = "#111111";
    content.strokeRect(400,400,100,100);//基于边框的矩形

    content.clearRect(425,425,50,50)//清除区域
	}


## 路径 ##

    var draw = document.getElementById("drawing");

	if(draw.getContext){
	    var content = draw.getContext("2d");
	
	    content.beginPath();//begin
	
	    content.arc(100,100,99,0,2 *Math.PI,false);//外圆
	
	    content.moveTo(194,100);//如果没有move的过程 两个圆的路径会连接
	    content.arc(100,100,94,0,2*Math.PI,false)
	
	    content.moveTo(100,100);
	    content.lineTo(100,15);
	
	    content.moveTo(100,100);
	    content.lineTo(15,100);
	    content.stroke()

	}


## 绘制文本 ##

    content.font = "bold 14px console";
    content.textAlign = "center";
    content.textBaseline = "middle";
    content.fillText("6",100,15);
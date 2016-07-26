#五子棋
分别使用Canvas和DOM实现的五子棋

**文件夹说明**
- FiveChess_Canvas为使用canvas实现的五子棋
测试链接：http://test.linptech.com/FiveChess_Canvas/index.html
- FiveChess_DOM为使用DOM实现的五子棋
测试链接： http://test.linptech.com/FiveChess_DOM/index.html
- FiveChess为使用模板方法重构的五子棋
测试链接：http://test.linptech.com/FiveChess/index.html

**重构思想：**
由于棋盘和棋子可以用Canvas和DOM两种方式实现，对应于画棋盘
`drawChessBoard( )`和画棋子`drawChess( )`都有canvas和dom两种策略可以实现，也就是说是基于多策略，首先可以想到使用策略模式来重构，而且应该也是可行的。不过进一步比较两种方案，会发现两种方案具有相同的行为，只是实现的方式不同，可以采用模板方法来对其重构。首先定义一个抽象父类Chess(位于chess.js)：
```javascript
/*定义一个抽象父类，实现一些公共方法以及封装子类中所有方法的执行顺序*/

var Chess=function(){};

Chess.prototype.drawChessBoard = function() {};//空方法应该由子类重写
Chess.prototype.drawChess = function() {};//空方法应该由子类重写

Chess.prototype.addEvent=function(){//公共方法,即添加点击事件
	var that=this;
	//获得棋盘到其包含元素的左边和右边距离
	var chessLeft=this.chess.offsetLeft;
	var chessTop=this.chess.offsetTop;
	this.chess.onclick=function(e){
		if (isOver()) {
			window.alert("本轮游戏已经结束了，请刷新浏览器重新开始！");
			return;
		}
		//获取点击的位置相当于棋盘左上角的位置
		var x=e.clientX-chessLeft;
		var y=e.clientY-chessTop;
		var i=Math.floor(x/30);
		var j=Math.floor(y/30);

		if(isEmpty(i,j)){//当鼠标点击处没有棋子时，画棋子并更新结果
			that.drawChess(i,j);
			updateResult(i,j);
		}		
	}
};


/*初始化
@para id 棋盘对应的dom元素id
*/
Chess.prototype.init=function(id){
	this.chess=document.getElementById(id);
	this.drawChessBoard();
	this.addEvent();
}
```
然后分别实现`CanvasChess`(位于canvasChess.js)和`DomChess`(位于domChess.js)子类.
同时，把判断棋子状态和输赢的业务放在juge.js.
```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>五子棋</title>
	<link rel="stylesheet" type="text/css" href="css/canvasChess.css">
	<!-- <link rel="stylesheet" type="text/css" href="css/domChess.css"> -->
	
</head>
<body>
	<h1>使用模板方法重构的五子棋</h1>
	<canvas id="chess" width="450px" height="450px;"></canvas>
	<!-- <div id="chess"></div> -->

	<script type="text/javascript" src="js/chess.js"></script>
	<script type="text/javascript" src="js/juge.js"></script>
	<script type="text/javascript" src="js/canvasChess.js"></script>
	<!-- <script type="text/javascript" src="js/domChess.js"></script> -->
	
</body>
</html>
```
在HTML的DOM结构中只需要切换注释的样式和元素标签以及 子类脚本就能快速从canvas实现转换层dom实现。在实际运营中，可以通过检测浏览器是否支持canvas特性，很方便的加载对应的结构。
<!DOCTYPE html>
<html>
<head>
	<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
	<meta name="viewport" content="initial-scale=1.0, user-scalable=no" />
	<style type="text/css">
	body, html,#allmap {width: 100%;height: 100%;overflow: hidden;margin:0;font-family:"微软雅黑";}
	</style>
	<script type="text/javascript" src="http://api.map.baidu.com/api?v=1.4"></script>
	<script type="text/javascript" src="http://api.map.baidu.com/library/DistanceTool/1.2/src/DistanceTool_min.js"></script>

	<script type="text/javascript" src="http://api.map.baidu.com/library/DrawingManager/1.4/src/DrawingManager_min.js"></script>
	<link rel="stylesheet" href="http://api.map.baidu.com/library/DrawingManager/1.4/src/DrawingManager_min.css" />
	<!--加载检索信息窗口-->
	<script type="text/javascript" src="http://api.map.baidu.com/library/SearchInfoWindow/1.4/src/SearchInfoWindow_min.js"></script>
	<link rel="stylesheet" href="http://api.map.baidu.com/library/SearchInfoWindow/1.4/src/SearchInfoWindow_min.css" />
	<title>地图展示</title>
</head>
<body>
    <label >地点：</label>
  <input id="where" name="where" type="text" value="西安市雁南五路曲江春晓苑">
  <input type="button" value="地图上找" onClick="sear();" />
  （getPoint：需要输入详细到街道的地址）</br>
  <label >地点：</label>
<input style="width:300px;" type="text" value="西安市雁南五路曲江春晓苑" id="keyword_1" /><input value="智能搜索" type="button" onclick="fun_search();" />（search：在指定城市或全国内搜索关键词。）</br>
<input type="button" value="清空" onclick="clearAllData()" />
	<div id="allmap" style="overflow:hidden;zoom:1;position:relative;">	
		<div id="map" style="height:100%;-webkit-transition: all 0.5s ease-in-out;transition: all 0.5s ease-in-out;"></div>
	</div>
	<script type="text/javascript">
	// 百度地图API功能
    var map = new BMap.Map("allmap");    // 创建Map实例
	map.centerAndZoom(new BMap.Point(116.404, 39.915), 11);  // 初始化地图,设置中心点坐标和地图级别
	map.addControl(new BMap.MapTypeControl());   //添加地图类型控件
	map.setCurrentCity("北京");          // 设置地图显示的城市 此项是必须设置的
	var myDis = new BMapLib.DistanceTool(map);
	map.enableScrollWheelZoom(true);     //开启鼠标滚轮缩放
	map.addControl(new BMap.NavigationControl());
    var overlays = [];
	var overlaycomplete = function(e){
        overlays.push(e.overlay);
        map.addOverlay(e.overlay);
        e.overlay.addEventListener("click",overlay_style);
    };

    function overlay_style(e){
		var p = e.target;
		if(p instanceof BMap.Marker){
			alert("该覆盖物是点，点的坐标是：" + p.getPosition().lng + "," + p.getPosition().lat);   
		}else if(p instanceof BMap.Circle){
			alert("该覆盖物是圆，圆的半径是：" + p.getRadius() + "，圆的中心点坐标是：" + p.getCenter().lng + "," + p.getCenter().lat);   
		}else if(p instanceof BMap.Polyline){
			alert("该覆盖物是折线，所画点的个数是：" + p.getPath().length);   
		}else{
			alert("无法获知该覆盖物类型");
		}
	}	

    var styleOptions = {
        strokeColor:"red",    //边线颜色。
        fillColor:"red",      //填充颜色。当参数为空时，圆形将没有填充效果。
        strokeWeight: 3,       //边线的宽度，以像素为单位。
        strokeOpacity: 0.8,	   //边线透明度，取值范围0 - 1。
        fillOpacity: 0.6,      //填充的透明度，取值范围0 - 1。
        strokeStyle: 'solid' //边线的样式，solid或dashed。
    }
    //实例化鼠标绘制工具
    var drawingManager = new BMapLib.DrawingManager(map, {
        isOpen: false, //是否开启绘制模式
        enableDrawingTool: true, //是否显示工具栏
        drawingToolOptions: {
            anchor: BMAP_ANCHOR_TOP_RIGHT, //位置
            offset: new BMap.Size(5, 5), //偏离值
        },
        circleOptions: styleOptions, //圆的样式
        polylineOptions: styleOptions, //线的样式
        polygonOptions: styleOptions, //多边形的样式
        rectangleOptions: styleOptions //矩形的样式
    });  
	 //添加鼠标绘制工具监听事件，用于获取绘制结果
    drawingManager.addEventListener('overlaycomplete', overlaycomplete);
    function clearAll() {
		for(var i = 0; i < overlays.length; i++){
            map.removeOverlay(overlays[i]);
        }
        overlays.length = 0   
    }

    function clearAllData() {
		map.clearOverlays();
	}

	var myGeo = new BMap.Geocoder();
	function sear(){
		var city = document.getElementById('where').value;
		if(city != ""){
			myGeo.getPoint(city, function(point){
      if (point) {
        map.centerAndZoom(point, 15);
        map.addOverlay(new BMap.Marker(point));
      }
    }, "全国");
		}
	}

	var options = {renderOptions: {map: map, panel: "results"}};
    var myLocalsearch = new BMap.LocalSearch(map,options);
     //模糊查询search方法
    function fun_search(){
        var value_keyword_1 = document.getElementById("keyword_1").value;
        myLocalsearch.search(value_keyword_1);
    }
</script>
</body>

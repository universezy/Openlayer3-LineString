关于LineString，官方的示例给的是鼠标画点，自动连线，并添加箭头（[LineString Arrows示例](http://openlayers.org/en/latest/examples/line-arrows.html?q=linestring)），而在我们实际应用中，往往需要手动录入标记点，然后进行连线并添加箭头，下面就分享我使用LineString的过程：

首先还是静态数据的：

先准备一个source图层用来画点：
````
var source = new ol.source.Vector();
````

然后是录入标记点的信息，所有点共同构成一个feature：
````
var feature = new ol.Feature({  
  				geometry:new ol.geom.LineString(coordinate1,coordinate2,coordinate3,coordinate4......)
  			});
````

然后把feature添加到source里：
````
source.addFeature(feature);
````

接下来准备一个图层用来画线和箭头：
````
var vector = new ol.layer.Vector({
    		source: source,
    		style: myStyle
    	});
````

这里的myStyle函数返回的是对线和箭头样式设置的style：
````
var myStyle = function(feature) {
    		var geometry = feature.getGeometry();
    		var styles = [
    		new ol.style.Style({
    			fill: new ol.style.Fill({
    				color: '#0044CC'
    			}), 
    			stroke: new ol.style.Stroke({  
    				lineDash:[1,2,3,4,5,6],
    				width: 3,  
    				color: [255, 0, 0, 1]  
    			})  
    		})
    		];

    		geometry.forEachSegment(function(start, end) {
    			var arrowLonLat = [(end[0]+start[0])/2,(end[1]+start[1])/2];
    			var dx = end[0]- start[0]; 
    			var dy = end[1] - start[1];
    			var rotation = Math.atan2(dy, dx);
    			styles.push(new ol.style.Style({
    				geometry: new ol.geom.Point(arrowLonLat),
    				image: new ol.style.Icon({
    					src: 'res/arrow.png',
    					anchor: [0.75, 0.5],
    					rotateWithView: true,
    					rotation: -rotation
    				})
    			}));
    		});
    		return styles;
    	};
````

函数里上面的styles就是线的样式设置，lineDash是设置虚线，下面的geometry是设置的箭头，需要计算旋转角度，我的箭头图片是一个朝右的三角形，arrowLonLat得到的线的起点和终点的中点。
然后把地图层和这个linestring的图层vector一起加到map的layers里就完成了。
接下里说动态添加新的标记点：
geometry可以设为全局变量：
````
var geometry = new ol.geom.LineString();
````

然后使用appendCoordinate添加点：
````
geometry.appendCoordinate(ol.proj.transform(newPoint, 'EPSG:4326', 'EPSG:3857'));
````

geometry设置好后，feature也就完成了，然后把之后的几个步骤中的变量更新一下就完成了。

附上效果图：
![](https://github.com/13608089849/Openlayer3-LineString/blob/master/image/linestring.png?raw=true)

[Github源码](https://github.com/13608089849/Openlayer3-LineString)

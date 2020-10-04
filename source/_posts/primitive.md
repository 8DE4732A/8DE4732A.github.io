---
title: 用几何原语再现图像
tags: javascript
date: 2020-10-04 08:28:13
categories:
mp3:
cover:
---

![](/assets/me_rect.jpg)
![](/assets/me_cricle.jpg)

```python
def draw_circle(startX, startY, radius, r, g, b):
    pu()
    colormode(255)
    pencolor(r, g, b)
    begin_fill()
    fillcolor((r, g, b))
    goto(startX, startY)
    pd()
    
    circle(radius)
    
    end_fill()
    pu()

def line(startX, startY, endX, endY, r, g, b):
    colormode(255)
    pencolor(r, g, b)
    begin_fill()
    fillcolor((r, g, b))
    goto(startX, startY)
    pd()
    forward(abs(endX - startX))
    right(90)
    forward(abs(endY - startY))
    right(90)
    forward(abs(endX - startX))
    right(90)
    forward(abs(endY - startY))
    right(90)
    
    end_fill()
    pu()

def rect(startX, startY, endX, endY, r, g, b):
    colormode(255)
    pencolor(r, g, b)
    begin_fill()
    fillcolor((r, g, b))
    goto(startX, startY)
    pd()
    forward(abs(endX - startX))
    right(90)
    forward(abs(endY - startY))
    right(90)
    forward(abs(endX - startX))
    right(90)
    forward(abs(endY - startY))
    right(90)
    
    end_fill()
    pu()

json = json.loads(jsonStr)
data = json["shapes"]

speed(0)

s0 = data[0]
pos = s0["data"]
color = s0["color"]
rect(pos[0]  - 250 , -pos[1] + 300 , pos[2]  - 250 , -pos[3]  + 300 , color[0], color[1], color[2])
for s in data[1:]:
    pos = s["data"]
    color = s["color"]
    draw_circle(pos[0] - 250 , -pos[1] + 300 - pos[2] , pos[2] , color[0], color[1], color[2])
```
![](/assets/me.jpg)
```javascript
var drawing=document.getElementById("myCanvas");
	if (drawing.getContext){
		var ctx = drawing.getContext("2d");
        var index = 0;
        function heart(){
		var arrays = data["shapes"];
		var pos = arrays[index]["data"];
		var color = arrays[index]["color"];
		var type = arrays[index]["type"];
		switch(type){
		case 1: 
		ctx.fillStyle = "rgba(" + color[0] + ", "+ color[1] + ",  "+ color[2] + ", " + color[3] / 255 + ")";
		ctx.fillRect (pos[0] , pos[1] ,(pos[2] - pos[0]) , (pos[3] - pos[1]));
		break;
		case 32:
          ctx.fillStyle = "rgba(" + color[0] + ", "+ color[1] + ",  "+ color[2] + ", " + color[3] / 255 + ")";
		   ctx.beginPath();
		   ctx.arc(pos[0] ,pos[1] ,pos[2] ,0,2*Math.PI);
		   ctx.fill();
		   break;
		   //case 64:
		   //ctx.strokeStyle="rgba(" + color[0] + ", "+ color[1] + ",  "+ color[2] + ", " + color[3] / 255 + ")";
		   //ctx.beginPath();
		   //ctx.moveTo(n.get_x1(a),n.get_y1(a));
		   //ctx.lineTo(n.get_x2(a),n.get_y2(a));
		   //ctx.stroke();
		   //break; -->
		   }
            index++;
            if (index > arrays.length){
                clearInterval(intervalId);
            }
        }
        intervalId = setInterval(heart,25);
	}
```

[https://github.com/fogleman/primitive](https://github.com/fogleman/primitive)

[https://www.samcodes.co.uk/project/geometrize-haxe-web/](https://www.samcodes.co.uk/project/geometrize-haxe-web/)
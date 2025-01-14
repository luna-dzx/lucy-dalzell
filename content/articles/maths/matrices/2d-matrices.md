+++
title = "2D Matrices"
description = ""
date = 2025-01-11
weight = 0

[extra]
headers = [
  { title = "Basics", id = "basics" },
  { title = "Matrices as Transformations", id = "matrices-as-transformations", subheaders = [
    { title = "Identity Matrix", id = "identity-matrix" },
    { title = "Scaling", id = "scaling" },
    { title = "45° Rotation", id = "45-deg-rotation" },
  ]},
  { title = "Matrix Multiplication", id = "matrix-multiplication", subheaders = [
    { title = "Rotating a Point", id = "rotating-a-point" },
    { title = "Rotating Multiple Points", id = "rotating-multiple-points" },
  ]},
  { title = "Further 2D Transformations", id = "further-2d-transformations", subheaders = [
    { title = "Rotation", id = "rotation" },
    { title = "Translation", id = "translation" },
  ]},
  { title = "Next", id = "next", optional = "yes"}
]
+++

---

# Basics
To define a position in 2D space, we use 2 scalar values:

[//]: # (p5.js 2D plot)
<script>
function setTextColor(p, flipStroke = false, mult=1.0) {
  
  let w = 255*mult;
  let b = 255 - w;

  if (isDarkMode) {
    p.fill(w,w,w);
    if (flipStroke) {
      p.stroke(b,b,b);
    } else {
      p.stroke(w,w,w);
    }
  }
  else {
    p.fill(b,b,b);
    if (flipStroke) {
      p.stroke(w,w,w);
    } else {
      p.stroke(b,b,b);
    }
  }
}

function draw2dPlotArrows(p, plotWidth, plotHeight, plotX=0, plotY=0, arrowWidth=10, margin=2) {
    p.strokeWeight(4);

    // y = green
    p.stroke(22,190,46);
    let endX = plotX+arrowWidth;
    let endY = plotY+margin;
    p.line(plotX+arrowWidth,   plotY+plotHeight-arrowWidth, endX, endY);
    p.line(plotX,              plotY+arrowWidth+margin,     endX, endY);
    p.line(plotX+2*arrowWidth, plotY+arrowWidth+margin,     endX, endY);

    // x = 
    p.stroke(250,32,32);
    endX = plotX + plotWidth - margin;
    endY = plotY + plotHeight - arrowWidth;
    p.line(plotX+arrowWidth,                  plotY+plotHeight-arrowWidth,   endX, endY);
    p.line(plotX+plotWidth-arrowWidth-margin, plotY+plotHeight,              endX, endY);
    p.line(plotX+plotWidth-arrowWidth-margin, plotY+plotHeight-2*arrowWidth, endX, endY);
}


   

function sketch_canvas2dBasics(p) {
  let darkModeCache = false;

  let WIDTH = 300;
  let HEIGHT = 300;

  p.setup = function () {
    p.createCanvas(WIDTH,HEIGHT);
    darkModeCache = !isDarkMode

    draw2dPlotArrows(p, WIDTH, HEIGHT);

    p.textSize(32);
  }
  p.draw = function () {
    if (darkModeCache == isDarkMode) { return; }

    p.strokeWeight(8);
    setTextColor(p);
    p.point(150,150);

    p.strokeWeight(0);
    p.text('p', 160,160)

    darkModeCache = isDarkMode;
  }
}
new p5(sketch_canvas2dBasics, 'canvas2dBasics');
</script>
<div class="center-flex">
  <div id="canvas2dBasics" style="width:150px;"></div>
</div>

$$
\definecolor{Red}{RGB}{250,32,32}
\definecolor{Green}{RGB}{22,190,46}
\definecolor{Blue}{RGB}{51,46,250}
\Large p=(\textcolor{Red}{x},\\, \textcolor{Green}{y})
$$
However 2 scalar values alone don't describe a position. \
We need to define which direction each of these scalars are pointing in.

Our position is actually defined as:
$$
\large
\begin{aligned}
p = x \textcolor{Red}{\begin{pmatrix} 1 \\\\ 0 \end{pmatrix}} + y \textcolor{Green}{\begin{pmatrix} 0 \\\\ 1 \end{pmatrix}}
\end{aligned}
$$
We now have the **"basis vectors"** of our 2D space, shown in red and green. \
The basis vectors represent the axes we work with, defining our space (the 2D plane).

Arranging these 2 vectors into a matrix gives us:
$$
\large
\begin{bmatrix}
  \textcolor{Red}{1} & \textcolor{Green}{0} \\\\
  \textcolor{Red}{0} & \textcolor{Green}{1}
\end{bmatrix}
$$
A matrix is simply a definition of a space. Reading the columns of a matrix gives us the basis vectors (i.e the axes).
So the complete definition of our original point $p$ is:
$$
\large
\begin{align}
p = 
\begin{bmatrix}
  \textcolor{Red}{1} & \textcolor{Green}{0} \\\\
  \textcolor{Red}{0} & \textcolor{Green}{1}
\end{bmatrix}
\circ \begin{pmatrix} x \\\\ y \end{pmatrix}
\end{align}
$$

---

# Matrices as Transformations
## Identity Matrix
The matrix formed in the last section is called the identity matrix:
$$
\large
\begin{align}
I_2 = \begin{bmatrix}
1 & 0 \\\\
0 & 1 \\\\
\end{bmatrix}
\end{align}
$$
The identity matrix represents a space with no transformation. \
Whenever we write a vector as a position, it is assumed that we are working in this space.
Here, the "2" denotes 2 dimensions.

## Scaling
We can change the basis vectors to transform our space about the origin. \
First, scaling the space is as simple as multiplying a basis vector by a scalar. \
For example, scaling by $2$ in the $x$ direction gives us:

$$
\large
\begin{aligned}
\textcolor{Red}{\begin{pmatrix} 2 \\\\ 0 \end{pmatrix}}, \textcolor{Green}{\begin{pmatrix} 0 \\\\ 1 \end{pmatrix}}
\rightarrow
\begin{bmatrix}
\textcolor{Red}{2} & \textcolor{Green}{0} \\\\
\textcolor{Red}{0} & \textcolor{Green}{1}
\end{bmatrix}
\end{aligned}
$$

2D space with the identity matrix:

<script>
function sketch_canvas2dScaling1(p) {
  let darkModeCache = false;

  let plotSize = 240;
  let gap = 80;

  let WIDTH = plotSize;
  let HEIGHT = plotSize;

  p.setup = function () {
    p.createCanvas(WIDTH,HEIGHT);
    darkModeCache = !isDarkMode

    draw2dPlotArrows(p, plotSize, plotSize);
    p.ellipseMode(p.CORNERS);
  }
  p.draw = function () {
    if (darkModeCache == isDarkMode) { return; }

    p.strokeWeight(4);
    setTextColor(p);
    p.noFill();
    p.ellipse(40, 40, plotSize-40, plotSize-40);

    darkModeCache = isDarkMode;
  }
}

function sketch_canvas2dScaling2(p) {
  let darkModeCache = false;

  let plotSize = 240;
  let gap = 80;

  let WIDTH = plotSize * 2;
  let HEIGHT = plotSize;

  p.setup = function () {
    p.createCanvas(WIDTH,HEIGHT);
    darkModeCache = !isDarkMode

    draw2dPlotArrows(p, plotSize*2, plotSize);
    p.ellipseMode(p.CORNERS);
  }
  p.draw = function () {
    if (darkModeCache == isDarkMode) { return; }

    p.strokeWeight(4);
    setTextColor(p);
    p.noFill();
    p.ellipse(40, 40, plotSize*2-40, plotSize-40);

    darkModeCache = isDarkMode;
  }
}

new p5(sketch_canvas2dScaling1, 'canvas2dScaling1');
new p5(sketch_canvas2dScaling2, 'canvas2dScaling2');
</script>
<div class="center-flex">
  <div id="canvas2dScaling1" style="width:120px;"></div>
</div>

$$
\large
\begin{bmatrix}
\textcolor{Red}{1} & \textcolor{Green}{0} \\\\
\textcolor{Red}{0} & \textcolor{Green}{1}
\end{bmatrix}
$$

2D space with $2x$ scaled matrix:

<div class="center-flex">
  <div id="canvas2dScaling2" style="width:240px;"></div>
</div>

$$
\large
\begin{bmatrix}
\textcolor{Red}{2} & \textcolor{Green}{0} \\\\
\textcolor{Red}{0} & \textcolor{Green}{1}
\end{bmatrix}
$$

## 45° Rotation
We can also change the direction of the basis vectors. \
To represent a rotation **without scaling**, we must find basis vectors of length $1$ for our rotated space.

For example, a $45^\text{o}$ anti-clockwise rotation has the following basis vectors:

<script>

function draw2dPlotCross(p, plotWidth, plotHeight, colors=false) {
  if (colors) {
    p.stroke(22,190,46); 
  }
  p.line(plotWidth * 0.5, 0, plotWidth * 0.5, plotHeight);

  if (colors) {
    p.stroke(250,32,32);
  }
  p.line(0, plotHeight*0.5, plotWidth, plotHeight*0.5);
}

function drawArrow(p, startX, startY, endX, endY, size=5) {
  let dir = p.createVector(startX-endX, startY-endY);
  dir = p5.Vector.setMag(dir, size);
  let altDir = p.createVector(dir.y, -dir.x);

  let arrowAlt1X = endX + dir.x + altDir.x;
  let arrowAlt1Y = endY + dir.y + altDir.y;

  let arrowAlt2X = endX + dir.x - altDir.x;
  let arrowAlt2Y = endY + dir.y - altDir.y;

  p.line(startX, startY, endX, endY);
  p.line(arrowAlt1X, arrowAlt1Y, endX, endY);
  p.line(arrowAlt2X, arrowAlt2Y, endX, endY);
}

function sketch_canvas2dRot45(p) {
  let darkModeCache = false;

  let WIDTH = 400;
  let HEIGHT = 400;

  // origin:
  let ox = WIDTH*0.5;
  let oy = HEIGHT*0.5;
  
  let arrowSize = 160;

  p.setup = function () {
    p.createCanvas(WIDTH,HEIGHT);
    darkModeCache = !isDarkMode
  }

  p.draw = function () {
    if (darkModeCache == isDarkMode) { return; }

    p.clear();
    p.strokeWeight(2);

    setTextColor(p, false, 0.75);
    draw2dPlotCross(p, WIDTH, HEIGHT);

    setTextColor(p, false, 0.9);

    p.strokeWeight(4);
    p.noFill();
    p.arc(ox,oy,100,100,-p.QUARTER_PI, 0);
    p.line(ox+20,oy-20, ox,oy-40);
    p.line(ox-20,oy-20, ox,oy-40);

    p.strokeWeight(0);
    p.textSize(30);

    p.text("45°", ox+30, oy-8);

    // red |1|
    p.fill(250,32,32)
    p.text("|1|", ox+arrowSize*0.5-30, oy-arrowSize*0.5-20);

    // green |1|
    p.fill(22,190,46);
    p.text("|1|", ox-arrowSize*0.5-50, oy-arrowSize*0.5+24);

    p.strokeWeight(4);

    p.stroke(250,32,32);
    drawArrow(p, ox, oy, ox + arrowSize, oy - arrowSize, 10);

    p.stroke(22,190,46);
    drawArrow(p, ox, oy, ox - arrowSize, oy - arrowSize, 10);

    p.ellipseMode(p.CENTER);


    darkModeCache = isDarkMode;
  }

}

new p5(sketch_canvas2dRot45, 'canvas2dRot45');
</script>
<div class="center-flex">
  <div id="canvas2dRot45" style="width: 200px;"></div>
</div>

*(Note that the basis vectors are length $1$, written as $|1|$)*

<script>
function sketch_canvas2dTrig45(p) {
  let darkModeCache = false;

  let chartSize = 310;
  let rAngleSize = 24;
  let arrowSize = 10;
  let smallTextSize = 24;
  let bigTextSize = 28;
  let arcSize = 100;
  let pad = 10;
  let margin = 60;
  let gap = 60;

  let WIDTH = 2 * (chartSize + margin) + gap;
  let HEIGHT = chartSize + margin;

  p.setup = function () {
    p.createCanvas(WIDTH,HEIGHT);
    darkModeCache = !isDarkMode
  }

  p.draw = function () {
    if (darkModeCache == isDarkMode) { return; }

    p.clear();
    setTextColor(p, false, 0.6);

    p.strokeWeight(4);
    p.textSize(smallTextSize);
    p.textAlign(p.LEFT);

    let x1 = margin + pad;
    let x2 = margin + chartSize-pad;
    let x3 = margin + chartSize+gap+pad;
    let x4 = WIDTH-pad-margin;

    let y1 = margin*0.5 + pad;
    let y2 = margin*0.5 + chartSize-pad;

    // left 
    p.line(x1,y1,x2,y1);
    p.line(x2,y1,x2,y2);

    // right
    p.line(x3,y2,x4,y2);
    p.line(x4,y1,x4,y2);

    setTextColor(p, false, 0.7);

    // right angles

    p.line(x4-rAngleSize,y2,x4-rAngleSize,y2-rAngleSize);
    p.line(x4-rAngleSize,y2-rAngleSize,x4,y2-rAngleSize);
    p.line(x2-rAngleSize, y1+rAngleSize, x2, y1+rAngleSize);
    p.line(x2-rAngleSize, y1+rAngleSize, x2-rAngleSize, y1);

    p.strokeWeight(4);

    p.noFill();
    p.arc(x3,y2,arcSize,arcSize,-p.QUARTER_PI, 0);
    p.arc(x2,y2,arcSize,arcSize,-p.QUARTER_PI*3, -p.HALF_PI);

    p.strokeWeight(0);
    setTextColor(p, false, 0.8);
    p.text("45°", x3+arcSize*0.5+5, y2-arcSize*0.15);
    p.text("45°", x2-arcSize*0.5, y2-arcSize*0.6);

    p.textSize(bigTextSize);
    p.textAlign(p.CENTER);

    p.text("1/√2", (x3+x4)*0.5 + bigTextSize/2, y2 + bigTextSize);
    p.text("1/√2", x4 + bigTextSize*1.5, (y1+y2)*0.5 + 5);
    p.text("1/√2", x2 + bigTextSize*1.5, (y1+y2)*0.5 + 5);
    p.text("-1/√2", (x1+x2)*0.5 + bigTextSize/2, y1 - 8);

    // green
    p.fill(22,190,46);
    p.text("|1|", (x1+x2)*0.5 - bigTextSize*0.5,(y1+y2)*0.5 + bigTextSize * 1.2);
    // red
    p.fill(250,32,32);
    p.text("|1|", (x3+x4)*0.5 - bigTextSize,(y1+y2)*0.5 - 2);

    p.strokeWeight(4);


    // green (left)
    p.stroke(22,190,46);
    drawArrow(p,x2,y2,x1,y1,arrowSize);

    // red (right)
    p.stroke(250,32,32);
    drawArrow(p,x3,y2,x4,y1,arrowSize);


    darkModeCache = isDarkMode;
  }

}

new p5(sketch_canvas2dTrig45, 'canvas2dTrig45');
</script>
<div class="center-flex">
  <div id="canvas2dTrig45" style="width:400px;"></div>
</div>

$$
\large
\begin{aligned}
\textcolor{Red}{
\begin{pmatrix} \frac{1}{\sqrt{2}} \\\\ \frac{1}{\sqrt{2}} \end{pmatrix}},
\textcolor{Green}{
\begin{pmatrix} \frac{-1}{\sqrt{2}} \\\\ \frac{1}{\sqrt{2}} \end{pmatrix}}
\rightarrow
\frac{1}{\sqrt{2}}
\begin{bmatrix}
\textcolor{Red}{1} & \textcolor{Green}{-1} \\\\
\textcolor{Red}{1} & \textcolor{Green}{1}
\end{bmatrix}
\end{aligned}
$$

---

# Matrix Multiplication
We've defined some basic transformations using matrices, but to carry out these transformations we must use matrix multiplication. \
The goal is to get a vector multiplied by the **identity matrix**, which will give us its position in normal 2D space.

## Rotating a Point
To rotate the point $\large (x, \\, y)$ by $45^\text{o}$, giving us $\large (x', \\, y')$, we can do the following:


<div class="math-container">

$$
\large
\begin{align}
I_2
\begin{pmatrix}
x' \\\\
y'
\end{pmatrix} =
\frac{1}{\sqrt{2}}
\begin{bmatrix}
\textcolor{Red}{1} & \textcolor{Green}{-1} \\\\
\textcolor{Red}{1} & \textcolor{Green}{1}
\end{bmatrix}
\circ \begin{pmatrix} x \\\\ y \end{pmatrix}
\end{align}
$$

</div>

To evaluate this, we can use the basis vectors of our rotation matrix: \
*(remember from the first section, $x$ and $y$ are just multiples of the basis vectors)*


<div class="math-container">

$$
\begin{align}
\begin{pmatrix}
x' \\\\
y'
\end{pmatrix} = 
\frac{1}{\sqrt{2}}
\(
x
\textcolor{Red}{
\begin{pmatrix} 1 \\\\ 1 \end{pmatrix}} + y
\textcolor{Green}{
\begin{pmatrix} -1 \\\\ 1 \end{pmatrix}}) =
\frac{1}{\sqrt{2}}
\begin{pmatrix}
\textcolor{Red}{1}x \textcolor{Green}{-1}y \\\\
\textcolor{Red}{1}x + \textcolor{Green}{1}y
\end{pmatrix}
\end{align}
$$

</div>

## Rotating Multiple Points
If we have multiple column vectors, we can multiply them using the same method.

<div class="math-container">

$$
\begin{align}
R_{45^\text{o}} =
\frac{1}{\sqrt{2}}
\begin{bmatrix}
\textcolor{Red}{1} & \textcolor{Green}{-1} \\\\
\textcolor{Red}{1} & \textcolor{Green}{1}
\end{bmatrix}
\circ \begin{pmatrix} 0 & 0 & 1 & 1 \\\\ 0 & 1 & 1 & 0 \end{pmatrix} =
\end{align}
$$
$$
\frac{1}{\sqrt{2}}
\begin{pmatrix}
(\textcolor{Red}{1} \times 0 \textcolor{Green}{ - 1} \times 0) &
(\textcolor{Red}{1} \times 0 \textcolor{Green}{ - 1} \times 1) &
(\textcolor{Red}{1} \times 1 \textcolor{Green}{ - 1} \times 1) &
(\textcolor{Red}{1} \times 1 \textcolor{Green}{ - 1} \times 0)
\\\\
(\textcolor{Red}{1} \times 0 \textcolor{Green}{ + 1} \times 0) &
(\textcolor{Red}{1} \times 0 \textcolor{Green}{ + 1} \times 1) &
(\textcolor{Red}{1} \times 1 \textcolor{Green}{ + 1} \times 1) &
(\textcolor{Red}{1} \times 1 \textcolor{Green}{ + 1} \times 0)
\end{pmatrix}
$$
$$
R_{45^\text{o}} =
\frac{1}{\sqrt{2}}
\begin{pmatrix} 0 & -1 & 0 & 1 \\\\ 0 & 1 & 2 & 1 \end{pmatrix}
$$


</div>

2D space with the identity matrix:

<script>
function sketch_canvas2dPointsBase(p) {
  let darkModeCache = false;

  let WIDTH = 400;
  let HEIGHT = 400;

  let ox = WIDTH*0.5;
  let oy = WIDTH*0.5;

  let size = 120;

  p.setup = function () {
    p.createCanvas(WIDTH,HEIGHT);
    darkModeCache = !isDarkMode
  }
  p.draw = function () {
    if (darkModeCache == isDarkMode) { return; }
    p.clear();
    p.strokeWeight(2);

    draw2dPlotCross(p, WIDTH, HEIGHT, colors=true);

    setTextColor(p, false, 0.55);

    p.strokeWeight(4);
    p.line(ox,oy, ox+size,oy);
    p.line(ox+size,oy, ox+size,oy-size);
    p.line(ox+size,oy-size, ox,oy-size);
    p.line(ox,oy-size, ox,oy);

    setTextColor(p);
    p.strokeWeight(10);
    p.point(ox,oy);
    p.point(ox+size,oy);
    p.point(ox,oy-size);
    p.point(ox+size,oy-size);

    darkModeCache = isDarkMode;
  }
}

new p5(sketch_canvas2dPointsBase, 'canvas2dPointsBase');
</script>
<div class="center-flex">
  <div id="canvas2dPointsBase" style="width:200px;"></div>
</div>


2D space with $R_{45^\text{0}}$:

<script>
function sketch_canvas2dPointsR45(p) {
  let darkModeCache = false;

  let WIDTH = 400;
  let HEIGHT = 400;

  let ox = WIDTH*0.5;
  let oy = WIDTH*0.5;

  let size = 120;

  p.setup = function () {
    p.createCanvas(WIDTH,HEIGHT);
    darkModeCache = !isDarkMode
  }
  p.draw = function () {
    if (darkModeCache == isDarkMode) { return; }
    p.clear();
    p.strokeWeight(2);

    draw2dPlotCross(p, WIDTH, HEIGHT, colors=true);

    setTextColor(p, false, 0.55);

    let d = 1/Math.sqrt(2);

    let x1 = ox;
    let y1 = oy;
    let x2 = ox-size*d;
    let y2 = oy-size*d;
    let x3 = ox;
    let y3 = oy-2*size*d;
    let x4 = ox+size*d;
    let y4 = oy-size*d;

    p.strokeWeight(4);
    p.line(x1,y1,x2,y2);
    p.line(x2,y2,x3,y3);
    p.line(x3,y3,x4,y4);
    p.line(x4,y4,x1,y1);

    setTextColor(p);
    p.strokeWeight(10);
    p.point(x1,y1);
    p.point(x2,y2);
    p.point(x3,y3);
    p.point(x4,y4);

    darkModeCache = isDarkMode;
  }
}

new p5(sketch_canvas2dPointsR45, 'canvas2dPointsR45');
</script>
<div class="center-flex">
  <div id="canvas2dPointsR45" style="width:200px;"></div>
</div>

In the second line of working out, you can see how the matrix is repeated 4 times, once for each point - matrix by matrix multiplication is just the same as matrix by vector multiplication.

---

# Further 2D Transformations
## Rotation
We can form a generalised rotation matrix with the same method as before:

<script>
function sketch_canvas2dTrig(p) {
  let darkModeCache = false;

  let chartSize = 290;
  let rAngleSize = 24;
  let arrowSize = 10;
  let smallTextSize = 26;
  let bigTextSize = 28;
  let arcSize = 100;
  let pad = 10;
  let margin = 80;
  let gap = 60;

  let WIDTH = 2 * (chartSize + margin) + gap;
  let HEIGHT = chartSize + margin;

  p.setup = function () {
    p.createCanvas(WIDTH,HEIGHT);
    darkModeCache = !isDarkMode
  }

  p.draw = function () {
    if (darkModeCache == isDarkMode) { return; }

    p.clear();
    setTextColor(p, false, 0.6);

    p.strokeWeight(4);
    p.textSize(smallTextSize);
    p.textAlign(p.LEFT);

    let x1 = margin + pad;
    let x2 = margin + chartSize-pad;
    let x3 = margin + chartSize+gap+pad;
    let x4 = WIDTH-pad-margin;

    let y1 = margin*0.5 + pad;
    let y2 = margin*0.5 + chartSize-pad;

    // left 
    p.line(x1,y1,x2,y1);
    p.line(x2,y1,x2,y2);

    // right
    p.line(x3,y2,x4,y2);
    p.line(x4,y1,x4,y2);

    setTextColor(p, false, 0.7);

    // right angles

    p.line(x4-rAngleSize,y2,x4-rAngleSize,y2-rAngleSize);
    p.line(x4-rAngleSize,y2-rAngleSize,x4,y2-rAngleSize);
    p.line(x2-rAngleSize, y1+rAngleSize, x2, y1+rAngleSize);
    p.line(x2-rAngleSize, y1+rAngleSize, x2-rAngleSize, y1);

    p.strokeWeight(4);

    p.noFill();
    p.arc(x3,y2,arcSize,arcSize,-p.QUARTER_PI, 0);
    p.arc(x2,y2,arcSize,arcSize,-p.QUARTER_PI*3, -p.HALF_PI);

    p.strokeWeight(0);
    setTextColor(p, false, 0.8);
    p.text("θ", x3+arcSize*0.5+5, y2-arcSize*0.15);
    p.text("θ", x2-arcSize*0.3, y2-arcSize*0.6);

    p.textSize(bigTextSize);
    p.textAlign(p.CENTER);

    p.text("cos(θ)", (x3+x4)*0.5 + bigTextSize/2, y2 + bigTextSize);
    p.text(" sin(θ)", x4 + bigTextSize*1.5, (y1+y2)*0.5 + 5);
    p.text(" cos(θ)", x2 + bigTextSize*1.5, (y1+y2)*0.5 + 5);
    p.text("-sin(θ)", (x1+x2)*0.5 + bigTextSize/2, y1 - 8);

    // green
    p.fill(22,190,46);
    p.text("|1|", (x1+x2)*0.5 - bigTextSize*0.5,(y1+y2)*0.5 + bigTextSize * 1.2);
    // red
    p.fill(250,32,32);
    p.text("|1|", (x3+x4)*0.5 - bigTextSize,(y1+y2)*0.5 - 2);

    p.strokeWeight(4);


    // green (left)
    p.stroke(22,190,46);
    drawArrow(p,x2,y2,x1,y1,arrowSize);

    // red (right)
    p.stroke(250,32,32);
    drawArrow(p,x3,y2,x4,y1,arrowSize);


    darkModeCache = isDarkMode;
  }
}

new p5(sketch_canvas2dTrig, 'canvas2dTrig');
</script>
<div class="center-flex">
  <div id="canvas2dTrig" style="width:400px;"></div>
</div>


$$
R_{\theta}=\begin{bmatrix}
\textcolor{Red}{\cos{\theta}} & \textcolor{Green}{-\sin{\theta}} \\\\
\textcolor{Red}{\sin{\theta}} & \textcolor{Green}{\cos{\theta}}
\end{bmatrix}
$$

## Translation
Unfortunately there is no way of changing the basis vectors such that the origin will move, however it is often useful to represent translation using a matrix in graphics programming. To represent translation, we need to borrow from the 3rd dimension. \
Suppose our 2D space lies at $\large z=1$.

<script>
function sketch_canvas2d3d(p) {
  let darkModeCache = false;

  let WIDTH = 400;
  let HEIGHT = 400;

  let ox = WIDTH*0.5;
  let oy = WIDTH*0.5;

  p.setup = function () {
    p.createCanvas(WIDTH,HEIGHT);
    darkModeCache = !isDarkMode
  }
  p.draw = function () {
    if (darkModeCache == isDarkMode) { return; }
    p.clear();

    // blue
    p.stroke(51,46,250);
    p.strokeWeight(4);
    drawArrow(p,20,HEIGHT-20, 170, HEIGHT-320, 10);

    p.strokeWeight(4);
    // red
    p.stroke(250,32,32);
    drawArrow(p,60,HEIGHT-100,60+290,HEIGHT-100, 10);

    // green
    p.stroke(22,190,46);
    drawArrow(p,60,HEIGHT-100,60,10, 10);


    setTextColor(p);
    p.strokeWeight(7);
    p.point(60,HEIGHT-100);

    p.textSize(28);

    p.strokeWeight(0);
    p.text("z=1",66,HEIGHT-100+34);

    darkModeCache = isDarkMode;
  }
}

new p5(sketch_canvas2d3d, 'canvas2d3d');
</script>
<div class="center-flex">
  <div id="canvas2d3d" style="width:200px;"></div>
</div>

By modifying the 3rd vector $\textcolor{Blue}{\begin{pmatrix}0 \\\\ 0\\\\ 1\end{pmatrix}}$ to add a translation of ${\begin{pmatrix}a \\\\ b \end{pmatrix}}$, we get the following space:

<script>
function sketch_canvas2d3dTrans(p) {
  let darkModeCache = false;

  let WIDTH = 400;
  let HEIGHT = 400;

  let ox = WIDTH*0.5;
  let oy = WIDTH*0.5;

  p.setup = function () {
    p.createCanvas(WIDTH,HEIGHT);
    darkModeCache = !isDarkMode
  }
  p.draw = function () {
    if (darkModeCache == isDarkMode) { return; }
    p.clear();

    setTextColor(p,false,0.4);
    p.strokeWeight(4);

    p.line(20,HEIGHT-20, 170, HEIGHT-320, 10);
    p.line(60,HEIGHT-100,60+290,HEIGHT-100, 10);
    p.line(60,HEIGHT-100,60,10, 10);

    // blue
    p.stroke(51,46,250);
    drawArrow(p,60-50,HEIGHT-100+50, 60+220,HEIGHT-100-220, 10);

    // red
    p.stroke(250,32,32);
    drawArrow(p,110,HEIGHT-150, 350,HEIGHT-150, 10);

    // green
    p.stroke(22,190,46);
    drawArrow(p,110,HEIGHT-150, 110,10, 10);

    setTextColor(p);
    p.strokeWeight(7);
    p.point(110,HEIGHT-150);

    p.textSize(28);

    p.strokeWeight(0);
    p.text("z=1",116,HEIGHT-150+34);

    darkModeCache = isDarkMode;
  }
}

new p5(sketch_canvas2d3dTrans, 'canvas2d3dTrans');
</script>
<div class="center-flex">
  <div id="canvas2d3dTrans" style="width:200px;"></div>
</div>



$$
\begin{bmatrix}
\textcolor{Red}{1} & \textcolor{Green}{0} & \textcolor{Blue}{a} \\\\
\textcolor{Red}{0} & \textcolor{Green}{1} & \textcolor{Blue}{b} \\\\
\textcolor{Red}{0} & \textcolor{Green}{0} & \textcolor{Blue}{1} \\\\
\end{bmatrix}
$$

This seems like a complex 3D transformation, but really since everything lies at $\textcolor{Blue}{z=1}$, we are just adding on $\textcolor{Blue}{1a}$ and $\textcolor{Blue}{1b}$ to $\large (\textcolor{Red}{x}, \\, \textcolor{Green}{y})$. \
When carrying out transformations using this matrix, we need to ensure we set $z=1$, for example:
$$
\begin{bmatrix}
\textcolor{Red}{1} & \textcolor{Green}{0} & \textcolor{Blue}{a} \\\\
\textcolor{Red}{0} & \textcolor{Green}{1} & \textcolor{Blue}{b} \\\\
\textcolor{Red}{0} & \textcolor{Green}{0} & \textcolor{Blue}{1} \\\\
\end{bmatrix}
\circ
\begin{pmatrix}
x \\\\
y \\\\
1 \\\\
\end{pmatrix} =
\begin{pmatrix}
x + \textcolor{Blue}{a} \\\\
y + \textcolor{Blue}{b} \\\\
1 \\\\
\end{pmatrix}
$$

---

# Next
## 3D Matrices

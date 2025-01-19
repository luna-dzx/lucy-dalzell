+++
title = "2D Signed Distance Functions"
date = 2025-01-14
description = ""
weight = 1

[extra]
headers = [
  { title = "Tricks for Deriving SDFs", id = "tricks-for-deriving-sdfs" },
  { title = "Circles", id = "circles" },
  { title = "Rectangles", id = "rectangles" },
]
+++

<script src="https://d3js.org/d3.v7.min.js"></script>

---

# Tricks for Deriving SDFs
Assume the centre of the shape lies at the origin. Translation can be applied later. (see SDF transformations). This lets us calculate distances from the centre of the shape to point $p$ as just $|p|$

If the shape is symmetrical around its centre, you can limit the space you're working in to simply positive values, and use the absolute value in your calculations. \
For example, if a shape is symmetrical in the $x$ axis, using $|x|$ in place of $x$ will yield the same result.


# Circles
A circle is defined by its radius $r$ (we are assuming it lies at the origin). \
The distance between the centre of the circle and point $p$ is $|p|$.

The signed distance, $d$, between the circle and $p$ is: 
$$
\large
d=|p|-r
$$
Drag point $p$ or the circle to view changes:

<div class="center-flex">
  <svg id="circle-sdf-svg" width="300" height="300" style="border: 1px var(--text-color) solid"
  viewBox="-150 -150 300 300"></svg>
</div>


<div class="center-flex">
  <div style="text-align: center; line-height: 5px;">
    <p id="circle-distance"></p>
    <p id="circle-radius"></p>
    <p id="circle-signed-distance"></p>
  </div>
</div>

<script>

function clamp(x, min, max) {
  return Math.max(Math.min(max, x), min);
}

{
  let px = 100.0;
  let py = -100.0;

  let cx = 0.0;
  let cy = 0.0;

  let r = 80.0;

  let pSize = 7.0;

  function sdf(x,y) {
    x = x-cx;
    y = y-cy;
    return Math.sqrt(x*x + y*y) - r;
  }

  function midPoint() {
    let v = [px-cx, py-cy];
    let len = Math.sqrt(v[0]*v[0] + v[1]*v[1]);
    v[0] *= r/len;
    v[1] *= r/len;

    return [cx + v[0], cy + v[1]];
  }

  let [mx, my] = midPoint();


  const svg = d3.select('#circle-sdf-svg');


  const guideLine = svg.append('line')
    .attr('x1', cx)
    .attr('y1', cy)
    .attr('x2', mx)
    .attr('y2', my)
    .attr('stroke', 'gray')
    .attr('stroke-width', 2)
    .attr('stroke-dasharray', "5,5");

  const line = svg.append('line')
      .attr('x1', mx)
      .attr('y1', my)
      .attr('x2', px)
      .attr('y2', py)
      .attr('stroke', 'var(--text-color)')
      .attr('stroke-width', 2);

  const sdIndicator = svg.append('circle')
      .attr('cx', px)
      .attr('cy', py)
      .attr('r', sdf(px,py))
      .attr('fill', 'transparent')
      .attr('stroke', 'gray')
      .attr('stroke-width', 2)
      .attr('stroke-dasharray', "5,5");

  const circle = svg.append('circle')
      .attr('cx', cx)
      .attr('cy', cy)
      .attr('r', r)
      .attr('stroke', 'var(--text-color)')
      .attr('stroke-width', 2)
      .attr('fill', 'transparent');

  const point = svg.append('circle')
      .attr('cx', px)
      .attr('cy', py)
      .attr('r', pSize)
      .attr('fill', 'blue')


  const pText = svg.append('text')
    .attr('x', px+10.0)
    .attr('y', py+10.0)
    .attr('fill', 'var(--text-color)')
    .text("p")
    .style('user-select','none');

  let distanceElem = document.getElementById('circle-distance');
  let radiusElem = document.getElementById('circle-radius')
  let radiusSlider = document.getElementById('circle-radius-slider');
  let signedDistanceElem = document.getElementById('circle-signed-distance');

  radiusElem.textContent = `r = ${Math.floor(r)} px`;



  function updateRadius(radius) {
    r = radius;
    radiusElem.textContent = `r = ${Math.floor(r)} px`;
    updateLine();
  }

  function updateLine() {

    px = Math.floor(point.attr('cx'));
    py = Math.floor(point.attr('cy'));

    [mx, my] = midPoint();
    const distance = sdf(px,py);

    sdIndicator
      .attr('cx', px)
      .attr('cy', py)
      .attr('r',Math.abs(distance));


    line.attr('x1', mx)
        .attr('y1', my)
        .attr('x2', px)
        .attr('y2', py);

    guideLine.attr('x2',mx)
             .attr('y2',my);

    pText.attr('x', px+10)
          .attr('y', py+10);

    if (distance < 0)
    {
      point.attr('fill', 'red');
    } else {
      point.attr('fill', 'blue')
    }

    distanceElem.textContent = `|p| = ${parseFloat(r + distance).toFixed(2)} px`;
    signedDistanceElem.textContent = `|p|-r = ${distance.toFixed(2)} px`;
  }

  function dragPoint(event) {
    point.attr('cx', clamp(event.x, -150 + pSize, 150 - pSize))
        .attr('cy', clamp(event.y, -150 + pSize, 150 - pSize));
    updateLine();
  }

  point.call(d3.drag()
    .on('start', function(event) {
      event.sourceEvent.preventDefault();
    })
    .on('drag', dragPoint)

  );

  circle.call(d3.drag()
    .on('start', function(event) {
      event.sourceEvent.preventDefault();
    })
    .on('drag', function(event) {

      let x = parseFloat(event.x);
      let y = parseFloat(event.y);
      let len = Math.sqrt(x*x + y*y);

      circle.attr('r', len);
      updateRadius(len);
    })
  );

  updateLine();
}
</script>

# Rectangles
A rectangle is defined by its width and height. For this, we will define a 2-component radius
$$
\large
R = ({width \over 2}, \\, {height \over 2})
$$

Rectangles are always symmetrical in both axes, so we only need to consider positive $x$ and $y$ values. \
We call our positive position $abs(p)$
$$
\large
p = (x, \\, y)
$$
$$
\large
\therefore abs(p) = (abs(x), \\, abs(y))
$$

Since we are working in $+x$, $+y$ we are only considering the top right corner. \
This corner will be at position $R$

We can now calculate the vector from this corner to $p$ with:
$$
\large
q = p - R
$$

If $p$ is inside the rectangle ($q_x<0$ and $q_y<0$): \
$d<0$. The distance is the shortest of $q_x$ and $q_y$. Since these are negative we take the maximum component:
$$ \large
d = maxcomp(q) 
$$

If $p$ is outside the rectangle ($q_x>=0$ or $q_y>=0$): \
$d>0$ \
If $q_y<0$, $d=q_x$ \
If $q_x<0$, $d=q_y$ \
Otherwise, $d=length(q)$

This can be simplified into:
$$ \large
q' = (max(q_x,0), \\, max(q_y,0)
$$
$$ \large
d = length(q')
$$

<div class="center-flex">
  <svg id="rect-sdf-svg" width="300" height="300" style="border: 1px var(--text-color) solid"
  viewBox="-150 -150 300 300"></svg>
</div>

<div class="center-flex">
  <div style="text-align: center; line-height: 5px;">
    <p><input type="checkbox" id="rect-show-symmetry">Show symmetry</input></p>
  </div>
</div>

<div class="center-flex">
  <div style="text-align: center; line-height: 5px;">
    <p id="rect-p"></p>
    <p id="rect-size"></p>
    <p id="rect-q"></p>
  </div>
</div>

<div class="center-flex">
  <div style="text-align: center; line-height: 5px;">
    <p id="rect-maxcomp-q"></p>
    <p id="rect-max-q0"></p>
    <p id="rect-len-q"></p>
  </div>
</div>

<script>
function clamp(x, min, max) {
  return Math.max(Math.min(max, x), min);
}

{
  let px = 100.0;
  let py = 100.0;

  let rx = -70;
  let ry = -50;
  let rw = -2 * rx;
  let rh = -2 * ry;

  let xRadius = -rx;
  let yRadius = -ry;

  let pSize = 7.0;

  function sdf(x,y) {

    let qx = Math.abs(x) - xRadius;
    let qy = Math.abs(y) - yRadius;

    let mq = Math.max(qx,qy);

    return Math.sqrt(Math.max(qx,0)**2 + Math.max(qy,0)**2) + Math.min(mq,0)
  }

  function midPoint(x,y) {
    let sd = sdf(x,y);
    let ax = Math.abs(x);
    let ay = Math.abs(y);

    let qx = ax - xRadius;
    let qy = ay - yRadius;


    if (ax <= xRadius && ay <= yRadius) {
      if (-qx < -qy) {
        if (x < 0.0) sd *= -1.0;
        return [x - sd, y];
      } else {
        if (y < 0.0) sd *= -1.0;
        return [x, y - sd];
      }
    }
    
    if (ax > xRadius && ay <= yRadius) {
      if (x < 0) {
        sd*=-1;
      }
      return [x - sd, y];
    }

    if (ax <= xRadius && ay > yRadius) {
      if (y < 0) {
        sd*=-1;
      }
      return [x, y - sd];
    }

    if (ax > xRadius && ay > yRadius) {
      let mx = rw*0.5;
      let my = rh*0.5;

      if (x < 0.0) mx *= -1.0;
      if (y < 0.0) my *= -1.0;

      return [mx,my]
    }

    return [x,y];

  }

  let [mx, my] = midPoint(px, py);

  const svg = d3.select('#rect-sdf-svg');

  const symLineX = svg.append('line')
      .attr('x1', -150)
      .attr('x2', 150)
      .attr('y1', 0)
      .attr('y2', 0)
      .attr('stroke', 'gray')
      .attr('stroke-width', 1);

  const symLineY = svg.append('line')
      .attr('y1', -150)
      .attr('y2', 150)
      .attr('x1', 0)
      .attr('x2', 0)
      .attr('stroke', 'gray')
      .attr('stroke-width', 1);

  const rect = svg.append('rect')
      .attr('x', rx)
      .attr('y', ry)
      .attr('width', rw)
      .attr('height', rh)
      .attr('stroke', 'var(--text-color)')
      .attr('stroke-width', 2)
      .attr('fill', 'transparent');

  const guideLine = svg.append('line')
    .attr('x1', 0)
    .attr('y1', 0)
    .attr('x2', mx)
    .attr('y2', my)
    .attr('stroke', 'gray')
    .attr('stroke-width', 2)
    .attr('stroke-dasharray', "5,5");


  const line = svg.append('line')
      .attr('x1', mx)
      .attr('y1', my)
      .attr('x2', px)
      .attr('y2', py)
      .attr('stroke', 'var(--text-color)')
      .attr('stroke-width', 2);

  const sdIndicator = svg.append('circle')
      .attr('cx', px)
      .attr('cy', py)
      .attr('r', sdf(px,py))
      .attr('fill', 'transparent')
      .attr('stroke', 'gray')
      .attr('stroke-width', 2)
      .attr('stroke-dasharray', "5,5");


  const symSdIndicator = svg.append('circle')
      .attr('cx', px)
      .attr('cy', py)
      .attr('r', sdf(px,py))
      .attr('fill', 'transparent')
      .attr('stroke', 'gray')
      .attr('stroke-width', 2)
      .attr('stroke-dasharray', "5,5");


  const absPoint = svg.append('circle')
      .attr('cx', Math.abs(px))
      .attr('cy', -Math.abs(py))
      .attr('r', pSize-1)
      .attr('fill', 'gray')

  const point = svg.append('circle')
      .attr('cx', px)
      .attr('cy', py)
      .attr('r', pSize)
      .attr('fill', 'blue')

  const pText = svg.append('text')
    .attr('x', px+10.0)
    .attr('y', py+10.0)
    .attr('fill', 'var(--text-color)')
    .text("p")
    .style('user-select','none');


  let showSymmetryElem = document.getElementById('rect-show-symmetry');
  let qElem = document.getElementById('rect-q');
  let pElem = document.getElementById('rect-p');
  let sizeElem = document.getElementById('rect-size');
  let maxCompElem = document.getElementById('rect-maxcomp-q');
  let maxQElem= document.getElementById('rect-max-q0');
  let lenQElem = document.getElementById('rect-len-q');


  let symmetryShown = false;

  function updateSymmetry(checked) {
    if (checked) {

      symLineX.attr('visibility','visible');
      symLineY.attr('visibility','visible');
      absPoint.attr('visibility','visible');
      symSdIndicator.attr('visibility','visible');

      symmetryShown = true;
    } else {

      symLineX.attr('visibility','hidden');
      symLineY.attr('visibility','hidden');
      absPoint.attr('visibility','hidden');
      symSdIndicator.attr('visibility','hidden');

      symmetryShown = false;
    }

    updateLine();
  }

  updateSymmetry(showSymmetryElem.checked);

  showSymmetryElem.addEventListener('change', (event) => {
    updateSymmetry(event.currentTarget.checked);
  })

  sizeElem.textContent = `R = (${Math.floor(xRadius)}, ${Math.floor(yRadius)})`
  pElem.textContent = `abs(p) = (${Math.abs(Math.floor(px))}, ${Math.abs(Math.floor(-py))})`;


  function updateSize(x,y) {
    let ax = Math.abs(Math.floor(x));
    let ay = Math.abs(Math.floor(y));

    rx = -ax;
    ry = -ay;

    rw = ax*2;
    rh = ay*2;

    xRadius = -rx;
    yRadius = -ry;

    sizeElem.textContent = `R = (${Math.floor(xRadius)}, ${Math.floor(yRadius)})`

    rect.attr('x', rx).attr('y', ry)
        .attr('width',rw).attr('height',rh);

    updateLine();

  }

  function updateLine() {

    px = Math.floor(point.attr('cx'));
    py = Math.floor(point.attr('cy'));

    const distance = sdf(px,py);

    let ax = Math.abs(px);
    let ay = -Math.abs(py);

    absPoint
      .attr('cx', ax)
      .attr('cy', ay);

    sdIndicator
      .attr('cx', px)
      .attr('cy', py)
      .attr('r',Math.abs(distance));

    if (symmetryShown) {

      [mx, my] = midPoint(ax,ay);

      line.attr('x1', mx)
          .attr('y1', my)
          .attr('x2', ax)
          .attr('y2', ay);

      guideLine.attr('x2',mx)
              .attr('y2',my);

      symSdIndicator
        .attr('cx', ax)
        .attr('cy', ay)
        .attr('r', Math.abs(distance));

    } else {

      [mx, my] = midPoint(px,py);

      line.attr('x1', mx)
          .attr('y1', my)
          .attr('x2', px)
          .attr('y2', py);

      guideLine.attr('x2',mx)
              .attr('y2',my)
    }



    pText.attr('x', px+10)
          .attr('y', py+10);

    if (distance < 0)
    {
      point.attr('fill', 'red');
    } else {
      point.attr('fill', 'blue')
    }

    let qx = Math.abs(px) - xRadius;
    let qy = Math.abs(py) - yRadius;

    qElem.textContent = `q = (${Math.floor(qx)}, ${Math.floor(qy)})`;
    pElem.textContent = `abs(p) = (${Math.abs(Math.floor(px))}, ${Math.abs(Math.floor(-py))})`;

    if (distance < 0) {

      maxQElem.textContent = null;
      maxCompElem.textContent = `maxcomp(q) = ${Math.max(qx,qy)}`
      lenQElem.textContent = `d = ${Math.floor(distance)}`

    } else {

      maxQElem.textContent = `q' = (${Math.max(qx,0)}, ${Math.max(qy,0)})`

      if (qx > 0 && qy > 0)
        lenQElem.textContent = `d = ${distance.toFixed(2)}`
      else
        lenQElem.textContent = `d = ${Math.floor(distance)}`

      maxCompElem.textContent = null;

    }

  }

  point.call(d3.drag()
    .on('start', function(event) {
      event.sourceEvent.preventDefault();
    point.attr('cx', clamp(event.x, -150 + pSize, 150 - pSize))
        .attr('cy', clamp(event.y, -150 + pSize, 150 - pSize));
    updateLine();
    })
    .on('drag', function(event) {
    point.attr('cx', clamp(event.x, -150 + pSize, 150 - pSize))
        .attr('cy', clamp(event.y, -150 + pSize, 150 - pSize));
    updateLine();
  }));

  rect.call(d3.drag()
    .on('start', function(event) {
      event.sourceEvent.preventDefault();

      updateSize(event.x,event.y);

    })
    .on('drag', function(event) {

      updateSize(event.x,event.y);
    })
  );

  updateLine();
}
</script>

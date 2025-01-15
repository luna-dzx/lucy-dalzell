+++
title = "2D Signed Distance Functions"
date = 2025-01-14
description = ""
weight = 1

[extra]
headers = [
  { title = "General Method", id = "general-method" },
  { title = "Circles", id = "circles" },
  { title = "Rectangles", id = "rectangles" },
]
+++

<script src="https://d3js.org/d3.v7.min.js"></script>

---

# General Method
1. Assume the centre of the shape lies at the origin. Translation can be applied later.(see SDF transformations)

2. If the shape is symmetrical around its centre, you can limit the space you're working in to simply positive values, and use the absolute value in your calculations. \
For example, if a shape is symmetrical in the $x$ axis, using $|x|$ in place of $x$ will yield the same result.

3. Find the closest point on the perimeter of the shape to a point $p$. We can call this point $c$.

...

# Circles
A circle is defined by its radius $r$ (we are assuming it lies at the origin). \
The distance between the centre of the circle and point $p$ is $|p|$.

The signed distance, $d$, between the circle and $p$ is the distance from the circle's centre minus the circle's radius:
$$
d=|p|-r
$$


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
  const circle = svg.append('circle')
      .attr('cx', cx)
      .attr('cy', cy)
      .attr('r', r)
      .attr('stroke', 'var(--text-color)')
      .attr('stroke-width', 2)
      .attr('fill', 'transparent');

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
  let signedDistanceElem = document.getElementById('circle-signed-distance');

  radiusElem.textContent = `r = ${r.toFixed(2)} px`;

  function updateLine() {

    px = parseFloat(point.attr('cx'));
    py = parseFloat(point.attr('cy'));

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

    distanceElem.textContent = `|p| = ${(r + distance).toFixed(2)} px`;
    signedDistanceElem.textContent = `|p|-r = ${distance.toFixed(2)} px`;
  }

  point.call(d3.drag()
    .on('start', function(event) {
      event.sourceEvent.preventDefault();
    })
    .on('drag', function(event) {
    point.attr('cx', clamp(event.x, -150 + pSize, 150 - pSize))
        .attr('cy', clamp(event.y, -150 + pSize, 150 - pSize));
    updateLine();
  }));

  updateLine();
}
</script>

# Rectangles
Rectangle

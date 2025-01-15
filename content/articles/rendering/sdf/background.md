+++
title = "What are SDFs?"
date = 2025-01-14
weight = 0
+++

---

# Signed Distance Functions
A Signed Distance Function (SDF) tells us the positive or negative (i.e. signed) distance from a position to a shape. \
A negative distance value means that this position is inside the shape.

Ideally, the magnitude of this value should be exactly the **minimum distance** that you would have to move from the input position to get to the edge of the object irrespective of direction.

<div class="center-flex">
  <img src="/images/signed-distance.png" style="width:300px;"></img>
</div>

$$
\definecolor{Red}{RGB}{250,32,32}
\definecolor{Green}{RGB}{22,190,46}
\definecolor{Blue}{RGB}{51,46,250}
\textcolor{Blue}{\text{positive}} \text{ and } \textcolor{Red}{\text{negative}} \text{ distance}
$$
$$
\text{(circle radius 5, lines at integer distances)}
$$



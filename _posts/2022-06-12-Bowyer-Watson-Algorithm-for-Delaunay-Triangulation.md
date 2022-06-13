---
layout: default
title: Bowyer-Watson Algorithm for Delaunay Triangulation
author: Ahmad Moussa
categories:
  - blog
description: The Bowyer-Watson algorithm for Delaunay triangulation is maybe one of the easier methods to implement and understand for obtaining a Delaunay Triangulation. This post serves as a step by step guide to coding up the algorithm from scratch.
thumbtype: img
thumbnail_path: https://gorillasun.de/assets/images/delaunay/banner.jpg
published: true
exclude_rss: true
legacy: false
listed: false
---

<div class="row gtr-50 gtr-uniform">
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/delaunay/color_triangulation.jpg" alt="">
		</span>
	</div>
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/delaunay/image_triangulation.jpg" alt="">
		</span>
	</div>
</div>

https://www.newcastle.edu.au/__data/assets/pdf_file/0017/22508/13_A-fast-algorithm-for-constructing-Delaunay-triangulations-in-the-plane.pdf

Before we begin with this post, I'd like to point out that a lot of research has gone into constructing Delaunay Triangulations. The intent of this post is solely to present a straightforward and popular method to constructing such a triangulation, as well as learning some useful techniques along the way. The Bowyer-Watson algorithm is well suited for making static artworks with a moderate amount of points. For creating animated or interactive triangulations I would recommend using one of the many efficient and highly optimized libraries that already exist for these purposes like <a href='https://github.com/mapbox/delaunator'>delaunator</a>. I'll include a short section how to use the delaunator at the end!

Lastly, before we begin, you can find a highly un-optimized version of the BW algo <a href='https://openprocessing.org/sketch/1587231'>here</a> which was essentially my initial version of the algo. This tutorial is loosely based on <a href='https://github.com/msavela'>Matias Savela</a>'s implementation which is quite digestible and very readable, and improves on the mistakes and implementation choices that I've made during my initial attempt! With this out of the way we can dive into the algorithm:

<h3>Index</h3>
1. Wikipedia's Bowyer-Watson Pseudocode Snippet
2. The algorithm
3. Implementation Details

<h3>Code</h3>
1. Vertex, Edge and Triangle Classes
2. The super Triangle
3. The triangulation procedure
4. Putting it all together

<h2>Wikipedia's Bowyer-Watson Pseudocode Snippet</h2>

After digging a bit on how to approach the triangulation task I came across a pseudocode snippet on the BW wikipedia page. It loosely describes the required steps of the algorithm:

[pseudocode snippet here]

Albeit translating pseudocode into executable code being a very good exercise, a lot of the implementation details are left for the coder to decide on. In many ways this pseudocode snippet can be further simplified. But let's first go through the algorithm step by step!

<h2>The algorithm</h2>

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/delaunay/proceduregif.gif" alt="">
</span>

The triangulation function in our case accepts as input a list of points. These will be the vertices from which we will create our future triangle mesh. An initial and necessary first step here, is to create a large triangle, which will contain within it's three edges, all of the points that ought to be triangulated. We call this triangle a 'super triangle'. Generally we assume this triangle to be infinite in size, however pragmatically we can make it just big enough to contain all of our points.

<div class="row gtr-50 gtr-uniform">
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/delaunay/0.jpg" alt="">
		</span>
	</div>
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/delaunay/1.jpg" alt="">
		</span>
	</div>
</div>

Having created this super triangle and placed our points we can begin constructing the triangulation. We will now iteratively go over each point that we have passed to the triangulation function to construct a valid triangulation. In the previous post we have discussed what makes a valid Delaunay Triangulation:

'No point is contained within the circumcircle of any triangle'

Thus we need to check if any of our points fall into the circumcircle of the already placed triangles. Initially this is simple, since we only have one triangle (the super triangle), which certainly contains all of the points within it's circumcircle.  We can mark this super triangle as invalid, and add it to an array that contains all of these invalid triangles. Again, we're still at the very beginning, hence the array will only contain the super triangle for now.

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/delaunay/2.jpg" alt="">
</span>

Next, we need to replace the invalid triangles by a different set of triangles that is valid. Initially this is simple, we simply form new triangles by connecting the edges of the super triangle to the initial point. Later on this becomes trickier because we need to determine the boundaries of the polygonal hole that is formed in the triangulation where we marked a number of triangles as invalid. We essentially need to fill the empty spot where we figuratively 'deleted' the bad triangles, by connecting the edges of this polygonal hole to the current point we're iterating over.

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/delaunay/3.jpg" alt="">
</span>

Before we end the current iteration we still need to remove all triangles that have been marked as invalid, and add the new triangles that we've formed by connecting the point with the edges of the polygonal hole. And we are ready for the next iteration.

In the second iteration we will add another point to the triangular mesh, and depending on it's position and the shape of the three triangles that we've created, it might fall into one or more circumcircles. If it does we need to mark those triangles as invalid, and we now need to find the polygonal hole again. The pseudocode snippet describes the polygonal hole as 'all edges in the set of bad triangles that is not shared between two bad triangles' which essentially means all the edges at the boundaries of that polygonal hole. Depending on how the code is implemented this can be more or less difficult. And again we form new triangles by connecting these unshared edges with the current point and adding these triangles to the triangulation.

<div class="row gtr-50 gtr-uniform">
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/delaunay/4.jpg" alt="">
		</span>
	</div>
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/delaunay/5.jpg" alt="">
		</span>
	</div>
</div>

In this manner we iteratively add vertices to this triangle mesh, subdividing the initial super triangle into smaller ones. Ultimately we will have achieved a valid Delaunay triangulation, in the final step we will need to remove all triangles that share an edge or a vertex with the initial super triangle, since they weren't part of the initial set of points.

<div class="row gtr-50 gtr-uniform">
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/delaunay/6.jpg" alt="">
		</span>
	</div>
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/delaunay/7.jpg" alt="">
		</span>
	</div>
</div>

<h2>Implementation Details</h2>

As mentioned earlier, a pseudocode snippet generally only tells you the major steps, but not about the smaller sub-routines that need to be incorporated to achieve these steps. For example, we should first write some robust code that can compute the circumcenter and circumcircle of a triangle since it is one of the cornerstones of this algorithm. Equivalently, implementing the vertex, edge and triangle information in a useful manner is crucial to save us many headaches along the way, especially since we need to make a large number of edge comparisons to find the polygonal hole.

The computation of the circumcenter and circumcircle will be discussed in a different post !!! here !!! because it is an interesting geometric problem on it's own outside the context of the Delaunay triangulation. In the post there's a number of ways to compute the circumcenter.



<h2>The Vertex, Edge and Triangle Classes</h2>

Let's start with writing the classes for the major three components of the triangulation: a vertex class, an edge class and a triangle class. The vertex object being the most basic one, only consisting of an x and y coordinates as well as a short comparison function:

<pre><code>// Vertex object
var Vertex = function(x, y) {
  this.x = x
  this.y = y
  this.equals = function(vertex){ return this.x === vertex.x && this.y == vertex.y }
}
</code></pre>

The edge object builds on top of the Vertex object. In this manner an edge essentially consists of two connected points, and can thus be represented by two vertices. It also has it's own comparison function. This function actually needs to do two comparisons to determine if an edge is equivalent to another one, this is due to the directionality of edges, which in this case, we don't really care for. Meaning that an edge going from vertex A to vertex B is the same as an edge going from vertex B to vertex A. Hence both cases need to be covered:

<pre><code>// Edge object
var Edge = function(v0, v1) {

  // The two vertices that make up the starting and ending vertex of the edge
  this.v0 = v0
  this.v1 = v1

  // Comparison function to compare this edge to another
  this.equals = function(edge){
    return (this.v0.equals(edge.v0) && this.v1.equals(edge.v1)) ||
      (this.v0.equals(edge.v1) && this.v1.equals(edge.v0));
  }
}
</code></pre>

This could be further optimised by assigning a ID to each edge and vertex and keeping track of them in a lookup table, saving us costly and redundant comparison operations. This will do for now however. Next up is the triangle object:

<pre><code>// Triangle object
var Triangle = function(v0, v1, v2) {
  this.v0 = v0
  this.v1 = v1
  this.v2 = v2
  this.circumCirc = calcCircumCirc(v0,v1,v2)
  this.inCircumcircle = function(v) {
    var dx = this.circumCirc.c.x - v.x;
    var dy = this.circumCirc.c.y - v.y;
    return Math.sqrt(dx * dx + dy * dy) <= this.circumCirc.r;
  }
};
</code></pre>

For the triangle object we don't require a comparison function (because we can compare it's edges), and the circumcenter computation we've outsourced. We just need some additional functionality to check if a given vertex is within the circumcircle of the triangle or not. Which can be done by calculating the distance from a given point to the circumcenter and checking if it less than it's radius.

<h2>The Super Triangle</h2>

This seems like a very simple part of the overall algorithm, but can also be considered an interesting problem. As said earlier, we could just make a triangle gigantic enough such that it encloses all of the points. For example, we choose all of our point coordinates to fall within the region that makes up the canvas,  and then make our triangle large enough to contain the entire canvas. In that case all of the points would certainly be inside the super-triangle.

A more interesting and much more difficult method would be finding a triangle that is just large enough to contain all points. There might even be problems with the first approach as detailed <a href='https://stackoverflow.com/questions/3642548/infinite-initial-bounding-triangle-in-iterative-delaunay-triangulators'>here </a> where the final triangulation doesn't end up being quite perfectly 'Delaunay'. In this sense we are looking at a minimum bounding box type of problem, and in this case it would be a minimum bounding triangle, one such algorithm that can compute this bounding triangle can be found <a href='https://link.springer.com/article/10.1007/s40314-014-0198-8'>here</a>. But this is a bit overkill as we would first have to compute the convex hull of the set of points, which makes things overly complicated when we can simply slap a big triangle around our points.

Here's Matias' method for finding the super-triangle. His function first finds the minimum and maximum vertex coordinates and then centers a triangle on top of all the points:

<pre><code>
// Triangle that bounds given vertices
var superTriangle = function(vertices) {
	var minx = miny = Infinity,
		maxx = maxy = -Infinity;
	vertices.forEach(function(vertex) {
		minx = Math.min(minx, vertex.x);
		miny = Math.min(minx, vertex.y);
		maxx = Math.max(maxx, vertex.x);
		maxy = Math.max(maxx, vertex.y);
	});

	var dx = (maxx - minx) * 10,
		dy = (maxy - miny) * 10;

	var v0 = new Vertex(minx - dx, miny - dy * 3),
		v1 = new Vertex(minx - dx, maxy + dy),
		v2 = new Vertex(maxx + dx * 3, maxy + dy);

	return new Triangle(v0, v1, v2);
};
</code></pre>

An alternative method which is maybe a bit more elegant, and maybe not as complicated as finding the minimum bounding triangle, is by finding the Minimum Enclosing Circle first, with Welzl's algorithm that conveniently runs in O(n) time, and then spanning a triangle around this enclosing circle. A javascript implementation of a minimum enclosing circle algo can be found <a href='https://www.nayuki.io/page/smallest-enclosing-circle'>here</a> by Project Nayuki. Then the enclosing triangle would simply be one that is spanned on a circle with twice the diameter of the minimum enclosing circle.

<h2>Triangulation Procedure</h2>

Here's an example of how the algorithm works on a larger set of points:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
// Reference: http://www.faqs.org/faqs/graphics/algorithms-faq/ Subject 1.04

function calcCircumCirc(v0, v1, v2) {
  var A = v1.x - v0.x;
  var B = v1.y - v0.y;
  var C = v2.x - v0.x;
  var D = v2.y - v0.y;

  var E = A * (v0.x + v1.x) + B * (v0.y + v1.y);
  var F = C * (v0.x + v2.x) + D * (v0.y + v2.y);

  var G = 2.0 * (A * (v2.y - v1.y) - B * (v2.x - v1.x));

  var dx, dy;

  // Collinear points, get extremes and use midpoint as center
  if (Math.round(Math.abs(G)) == 0) {
    var minx = Math.min(v0.x, v1.x, v2.x);
    var miny = Math.min(v0.y, v1.y, v2.y);
    var maxx = Math.max(v0.x, v1.x, v2.x);
    var maxy = Math.max(v0.y, v1.y, v2.y);

    center = new Vertex((minx + maxx) / 2, (miny + maxy) / 2);

    dx = center.x - minx;
    dy = center.y - miny;
  } else {
    var cx = (D * E - B * F) / G;
    var cy = (A * F - C * E) / G;

    center = new Vertex(cx, cy);

    dx = center.x - v0.x;
    dy = center.y - v0.y;
  }
  radius = Math.sqrt(dx * dx + dy * dy);

  return {c: center, r: radius}
}


function getBounding2(enclosing){
  ang1 = 0
  ang2 = TAU/3
  ang3 = TAU/3*2

  let ev0 = new Vertex(enclosing.x + enclosing.r*cos(ang1)*2,
          enclosing.y + enclosing.r*sin(ang1)*2)
  let ev1 = new Vertex(enclosing.x + enclosing.r*cos(ang2)*2,
          enclosing.y + enclosing.r*sin(ang2)*2)
  let ev2 = new Vertex(enclosing.x + enclosing.r*cos(ang3)*2,
          enclosing.y + enclosing.r*sin(ang3)*2)

  return new Triangle(ev0, ev1, ev2)
}

function getPerpendicular(p1, p2) {
  let slope = (p2.y - p1.y) / (p2.x - p1.x) + 0.0000000001
  let reciprocal = -1 / slope
  let b = (p1.y + p2.y) / 2 - (p1.x + p2.x) / 2 * reciprocal

  return {
    m: reciprocal,
    b: b
  };
}


// line intercept math by Paul Bourke http://paulbourke.net/geometry/pointlineplane/
// Determine the intersection point of two line segments
// Return FALSE if the lines don't intersect
function intersect(p1, p2, p3, p4) {
  let x1 = p1.x,
    y1 = p1.y,
    x2 = p2.x,
    y2 = p2.y,
    x3 = p3.x,
    y3 = p3.y,
    x4 = p4.x,
    y4 = p4.y;

  // Check if none of the lines are of length 0
  if ((x1 === x2 && y1 === y2) || (x3 === x4 && y3 === y4)) {
    return false
  }

  denominator = ((y4 - y3) * (x2 - x1) - (x4 - x3) * (y2 - y1))

  // Lines are parallel
  if (denominator === 0) {
    return false
  }

  let ua = ((x4 - x3) * (y1 - y3) - (y4 - y3) * (x1 - x3)) / denominator
  let ub = ((x2 - x1) * (y1 - y3) - (y2 - y1) * (x1 - x3)) / denominator

  /* this part can be omitted becasue we assume that the lines expand to infinity */

  // // is the intersection along the segments
  // if (ua < 0 || ua > 1 || ub < 0 || ub > 1) {
  // 	return false
  // }

  // Return a object with the x and y coordinates of the intersection
  let x = x1 + ua * (x2 - x1)
  let y = y1 + ua * (y2 - y1)

  return {
    x,
    y
  }
}


/*
 * Smallest enclosing circle - Library (compiled from TypeScript)
 *
 * Copyright (c) 2022 Project Nayuki
 * https://www.nayuki.io/page/smallest-enclosing-circle
 *
 * This program is free software: you can redistribute it and/or modify
 * it under the terms of the GNU Lesser General Public License as published by
 * the Free Software Foundation, either version 3 of the License, or
 * (at your option) any later version.
 *
 * This program is distributed in the hope that it will be useful,
 * but WITHOUT ANY WARRANTY; without even the implied warranty of
 * MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
 * GNU Lesser General Public License for more details.
 *
 * You should have received a copy of the GNU Lesser General Public License
 * along with this program (see COPYING.txt and COPYING.LESSER.txt).
 * If not, see <http://www.gnu.org/licenses/>.
 */
"use strict";
var Point = /** @class */ (function () {
    function Point(x, y) {
        this.x = x;
        this.y = y;
    }
    return Point;
}());
var Circle = /** @class */ (function () {
    function Circle(x, y, r) {
        this.x = x;
        this.y = y;
        this.r = r;
    }
    return Circle;
}());
/*
 * Returns the smallest circle that encloses all the given points. Runs in expected O(n) time, randomized.
 * Note: If 0 points are given, null is returned. If 1 point is given, a circle of radius 0 is returned.
 */
// Initially: No boundary points known
function makeCircle(points) {
    // Clone list to preserve the caller's data, do Durstenfeld shuffle
    var shuffled = points.slice();
    for (var i = points.length - 1; i >= 0; i--) {
        var j = Math.floor(Math.random() * (i + 1));
        j = Math.max(Math.min(j, i), 0);
        var temp = shuffled[i];
        shuffled[i] = shuffled[j];
        shuffled[j] = temp;
    }
    // Progressively add points to circle or recompute circle
    var c = null;
    shuffled.forEach(function (p, i) {
        if (c === null || !isInCircle(c, p))
            c = makeCircleOnePoint(shuffled.slice(0, i + 1), p);
    });

    return c;
}
// One boundary point known
function makeCircleOnePoint(points, p) {
    var c = new Circle(p.x, p.y, 0);
    points.forEach(function (q, i) {
        if (!isInCircle(c, q)) {
            if (c.r == 0)
                c = makeDiameter(p, q);
            else
                c = makeCircleTwoPoints(points.slice(0, i + 1), p, q);
        }
    });
    return c;
}
// Two boundary points known
function makeCircleTwoPoints(points, p, q) {
    var circ = makeDiameter(p, q);
    var left = null;
    var right = null;
    // For each point not in the two-point circle
    for (var _i = 0, points_1 = points; _i < points_1.length; _i++) {
        var r = points_1[_i];
        if (isInCircle(circ, r))
            continue;
        // Form a circumcircle and classify it on left or right side
        var cross = crossProduct(p.x, p.y, q.x, q.y, r.x, r.y);
        var c = makeCircumcircle(p, q, r);
        if (c === null)
            continue;
        else if (cross > 0 && (left === null || crossProduct(p.x, p.y, q.x, q.y, c.x, c.y) > crossProduct(p.x, p.y, q.x, q.y, left.x, left.y)))
            left = c;
        else if (cross < 0 && (right === null || crossProduct(p.x, p.y, q.x, q.y, c.x, c.y) < crossProduct(p.x, p.y, q.x, q.y, right.x, right.y)))
            right = c;
    }
    // Select which circle to return
    if (left === null && right === null)
        return circ;
    else if (left === null && right !== null)
        return right;
    else if (left !== null && right === null)
        return left;
    else if (left !== null && right !== null)
        return left.r <= right.r ? left : right;
    else
        throw new Error("Assertion error");
}
function makeDiameter(a, b) {
    var cx = (a.x + b.x) / 2;
    var cy = (a.y + b.y) / 2;
    var r0 = distance(cx, cy, a.x, a.y);
    var r1 = distance(cx, cy, b.x, b.y);
    return new Circle(cx, cy, Math.max(r0, r1));
}
function makeCircumcircle(a, b, c) {
    // Mathematical algorithm from Wikipedia: Circumscribed circle
    var ox = (Math.min(a.x, b.x, c.x) + Math.max(a.x, b.x, c.x)) / 2;
    var oy = (Math.min(a.y, b.y, c.y) + Math.max(a.y, b.y, c.y)) / 2;
    var ax = a.x - ox;
    var ay = a.y - oy;
    var bx = b.x - ox;
    var by = b.y - oy;
    var cx = c.x - ox;
    var cy = c.y - oy;
    var d = (ax * (by - cy) + bx * (cy - ay) + cx * (ay - by)) * 2;
    if (d == 0)
        return null;
    var x = ox + ((ax * ax + ay * ay) * (by - cy) + (bx * bx + by * by) * (cy - ay) + (cx * cx + cy * cy) * (ay - by)) / d;
    var y = oy + ((ax * ax + ay * ay) * (cx - bx) + (bx * bx + by * by) * (ax - cx) + (cx * cx + cy * cy) * (bx - ax)) / d;
    var ra = distance(x, y, a.x, a.y);
    var rb = distance(x, y, b.x, b.y);
    var rc = distance(x, y, c.x, c.y);
    return new Circle(x, y, Math.max(ra, rb, rc));
}
/* Simple mathematical functions */
var MULTIPLICATIVE_EPSILON = 1 + 1e-14;
function isInCircle(c, p) {
    return c !== null && distance(p.x, p.y, c.x, c.y) <= c.r * MULTIPLICATIVE_EPSILON;
}
// Returns twice the signed area of the triangle defined by (x0, y0), (x1, y1), (x2, y2).
function crossProduct(x0, y0, x1, y1, x2, y2) {
    return (x1 - x0) * (y2 - y0) - (y1 - y0) * (x2 - x0);
}
function distance(x0, y0, x1, y1) {
    return Math.hypot(x0 - x1, y0 - y1);
}
if (!("hypot" in Math)) // Polyfill
    Math.hypot = function (x, y) { return Math.sqrt(x * x + y * y); };


var Vertex = function(x, y) {
  this.x = x
  this.y = y
  this.equals = function(vertex){ return this.x === vertex.x && this.y == vertex.y }
}

var Edge = function(v0, v1) {
  this.v0 = v0
  this.v1 = v1
  this.equals = function(edge){
    return (this.v0.equals(edge.v0) && this.v1.equals(edge.v1)) ||
      (this.v0.equals(edge.v1) && this.v1.equals(edge.v0));
  }
}

// Triangle
var Triangle = function(v0, v1, v2) {
  this.v0 = v0
  this.v1 = v1
  this.v2 = v2
  this.circumCirc = calcCircumCirc(v0,v1,v2)
  this.inCircumcircle = function(v) {
    var dx = this.circumCirc.c.x - v.x;
    var dy = this.circumCirc.c.y - v.y;
    return Math.sqrt(dx * dx + dy * dy) <= this.circumCirc.r;
  }
};

// Remove duplicate edges
var uniqueEdges = function(edges) {
  var uniqueEdges = [];
  for (var i = 0; i < edges.length; ++i) {
    var isUnique = true;

    // See if edge is unique
    for (var j = 0; j < edges.length; ++j) {
      if (i != j && edges[i].equals(edges[j])) {
        isUnique = false;
        break;
      }
    }

    // Edge is unique, add to unique edges array
    isUnique && uniqueEdges.push(edges[i]);
  }

  return uniqueEdges;
};

// Update array of triangles by adding a new vertex
var addVertex = function(vertex, triangles) {
  var edges = [];

  // Remove triangles with circumcircles containing the vertex
  triangles = triangles.filter(function(triangle) {
    if (triangle.inCircumcircle(vertex)) {
      edges.push(new Edge(triangle.v0, triangle.v1));
      edges.push(new Edge(triangle.v1, triangle.v2));
      edges.push(new Edge(triangle.v2, triangle.v0));
      return false;
    }
    return true;
  });


  // Get unique edges
  edges = uniqueEdges(edges);

  // Create new triangles from the unique edges and new vertex
  edges.forEach(function(edge) {
    triangles.push(new Triangle(edge.v0, edge.v1, vertex));
  });


  return triangles;
};


function triangulate(vertices, stopi) {
  // Create bounding 'super' triangle
  var st = getBounding2(e) //superTriangle(vertices);

  // Initialize triangles while adding bounding triangle
  var triangles = [st];

  for(let n = 0; n < stopi; n++){
    triangles = addVertex(vertices[n], triangles);
    if(n == stopi){
      break
    }
  }

  //Remove triangles that share edges with super triangle
  triangles = triangles.filter(function(triangle) {
    return !(triangle.v0 == st.v0 || triangle.v0 == st.v1 || triangle.v0 == st.v2 ||
      triangle.v1 == st.v0 || triangle.v1 == st.v1 || triangle.v1 == st.v2 ||
      triangle.v2 == st.v0 || triangle.v2 == st.v1 || triangle.v2 == st.v2);
  });

  return triangles;
};

function setup() {
  frameRate(60)
  //randomSeed(1000)
  w = min(windowWidth, windowHeight)
  wx = w
  wy = w
  createCanvas(wx, wy)

  pad = w/12

  background(0)
  stroke(255)
  noFill()
  strokeWeight(5)
  strokeJoin(ROUND)

  //randomSeed(1000)
  pointList = []
  pointList2 = []
  for (let n = 0; n < 300; n++) {

    r = w*2 * sqrt(random())
    theta = random() * 2 * PI

    randX = random(pad, w-pad) //w/2 + r*cos(theta)
    randY = random(pad, w-pad) //w/2 + r*sin(theta)

    pointList.push(
      new Vertex(randX, randY)
    )
    pointList2.push(
      new Point(randX, randY)
    )

  }

  strokeWeight(1)
  e = makeCircle(pointList)
  //ellipse(e.x, e.y, e.r*2)

}

function draw(){
  background(0)
  strokeWeight(5)
  for(let j = 0; j < pointList.length; j++){
    point(pointList[j].x, pointList[j].y)
  }

  triangles = triangulate(pointList, frameCount)

  strokeWeight(1)
  stroke(255)
  triangles.forEach(
    (tri, i) => {

      beginShape()
      vertex(tri.v0.x, tri.v0.y)
      vertex(tri.v1.x, tri.v1.y)
      vertex(tri.v2.x, tri.v2.y)

      endShape(CLOSE)
    }
  )

  for(let j = 0; j < pointList.length; j++){
    point(pointList[j].x, pointList[j].y)

  }

  if(frameCount>299){
    noLoop()
  }
}
</script>
<p></p>

With all of the previous part in place, the remainder of the triangulation procedure becomes very straightforward! In this section we will iteratively add the triangulation vertices to the triangulation mesh and resolve the areas of conflict where vertices fall into the circumcircle of other triangles. This is done by removing the conflicting triangles, in turn creating a polygonal hole that can be patched up with a set of new triangles, by connecting the vacant edges with the current vertex in question. Doing this exactly as described by the pseudocode snippet may end up being a very inefficient way to do it, rather than comparing every triangle's edges against every other triangle's edges, we may actually just compare all edges and store the ones that are unique in a separate array to construct the polygonal hole.

With these considerations, we can model our procedure as follows:

<pre><code>
function triangulate(vertices) {
  // Create bounding 'super' triangle
  var st = makeSuperTriangle(getEnclosingCircle(pointList));

  // Initialize triangles while adding bounding triangle
  var triangles = [st];

  // Triangulate each vertex
  vertices.forEach(function(vertex) {
    triangles = addVertex(vertex, triangles);
  });

  // Remove triangles that share edges with super triangle
  triangles = triangles.filter(function(triangle) {
    return !(triangle.v0 == st.v0 || triangle.v0 == st.v1 || triangle.v0 == st.v2 ||
      triangle.v1 == st.v0 || triangle.v1 == st.v1 || triangle.v1 == st.v2 ||
      triangle.v2 == st.v0 || triangle.v2 == st.v1 || triangle.v2 == st.v2);
  });

  return triangles;
};
</code></pre>

Essentially we first create the super triangle as discussed earlier, and add it to the triangulation to get things rolling initially. Then we add one vertex at a time and do the bulk of the procedure in the addVertex() function. As a final step we also need to remove all triangles that share an edge with the original super triangle, since it was never part of the triangulation. As for the addVertex() function:

<pre><code>// Update array of triangles by adding a new vertex
var addVertex = function(vertex, triangles) {
  var edges = [];

  // Remove triangles with circumcircles containing the vertex
  triangles = triangles.filter(function(triangle) {
    if (triangle.inCircumcircle(vertex)) {
      edges.push(new Edge(triangle.v0, triangle.v1));
      edges.push(new Edge(triangle.v1, triangle.v2));
      edges.push(new Edge(triangle.v2, triangle.v0));
      return false;
    }
    return true;
  });

  // Get unique edges
  edges = uniqueEdges(edges);

  // Create new triangles from the unique edges and new vertex
  edges.forEach(function(edge) {
    triangles.push(new Triangle(edge.v0, edge.v1, vertex));
  });

  return triangles;
};
</code></pre>

Here we can conveniently make use of the <a href='https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter'>filter()</a> function that is applicable to arrays in javascript. The filter function creates a new array from all the elements that pass the condition that it's called upon. In this case the condition is actually another function that checks if the vertex falls into the circumcircle of the triangle, if it does we take the edges of this triangle and store them in the edges array, we'll need them in a second. If the vertex is not in the circumcircle, we can just ignore it as it is a valid triangle. Having obtained the edges, we now need to check which ones are unique, remember, as explained earlier, the edges that are not shared are those that fall onto the boundary of the polygonal hole. We do it in this manner, with the uniqueEdges() function:

<pre><code>// Remove duplicate edges
var uniqueEdges = function(edges) {
  var uniqueEdges = [];
  for (var i = 0; i < edges.length; ++i) {
    var isUnique = true;

    // See if edge is unique
    for (var j = 0; j < edges.length; ++j) {
      if (i != j && edges[i].equals(edges[j])) {
        isUnique = false;
        break;
      }
    }

    // Edge is unique, add to unique edges array
    isUnique && uniqueEdges.push(edges[i]);
  }

  return uniqueEdges;
};
</code></pre>

With a nested for loop we determine for each edge wether it is unique or not. In the outer loop we set a flag for the current edge, assuming that it is unique. During the nested loop where we make another pass over the array where we compare that edge to all other edges (except at the same loop index), if we find a duplicate we set the flag to false. Once this inner loop completes, we check the flag, and only in the case of it being unique we store in an array uniqueEdges which will be returned to the addVertex() function. Those unique edges then make up the boundary of the polygonal hole that needs to be re-triangulated. This iterative procedure thus eliminates all invalid triangles and re-triangulates them into smaller ones until we obtain a valid Delaunay Mesh!

<h2>Putting it all together!</h2>

And finally let's put everything together! The final code ends up being relatively long, so here's a working example using p5js (I've included everything required like the circumcircle computation as well):

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
// Reference: http://www.faqs.org/faqs/graphics/algorithms-faq/ Subject 1.04

function calcCircumCirc(v0, v1, v2) {
  var A = v1.x - v0.x;
  var B = v1.y - v0.y;
  var C = v2.x - v0.x;
  var D = v2.y - v0.y;

  var E = A * (v0.x + v1.x) + B * (v0.y + v1.y);
  var F = C * (v0.x + v2.x) + D * (v0.y + v2.y);

  var G = 2.0 * (A * (v2.y - v1.y) - B * (v2.x - v1.x));

  var dx, dy;

  // Collinear points, get extremes and use midpoint as center
  if (Math.round(Math.abs(G)) == 0) {
    var minx = Math.min(v0.x, v1.x, v2.x);
    var miny = Math.min(v0.y, v1.y, v2.y);
    var maxx = Math.max(v0.x, v1.x, v2.x);
    var maxy = Math.max(v0.y, v1.y, v2.y);

    center = new Vertex((minx + maxx) / 2, (miny + maxy) / 2);

    dx = center.x - minx;
    dy = center.y - miny;
  } else {
    var cx = (D * E - B * F) / G;
    var cy = (A * F - C * E) / G;

    center = new Vertex(cx, cy);

    dx = center.x - v0.x;
    dy = center.y - v0.y;
  }
  radius = Math.sqrt(dx * dx + dy * dy);

  return {c: center, r: radius}
}


function getBounding2(enclosing){
  ang1 = 0
  ang2 = TAU/3
  ang3 = TAU/3*2

  let ev0 = new Vertex(enclosing.x + enclosing.r*cos(ang1)*2,
          enclosing.y + enclosing.r*sin(ang1)*2)
  let ev1 = new Vertex(enclosing.x + enclosing.r*cos(ang2)*2,
          enclosing.y + enclosing.r*sin(ang2)*2)
  let ev2 = new Vertex(enclosing.x + enclosing.r*cos(ang3)*2,
          enclosing.y + enclosing.r*sin(ang3)*2)

  return new Triangle(ev0, ev1, ev2)
}

function getPerpendicular(p1, p2) {
  let slope = (p2.y - p1.y) / (p2.x - p1.x) + 0.0000000001
  let reciprocal = -1 / slope
  let b = (p1.y + p2.y) / 2 - (p1.x + p2.x) / 2 * reciprocal

  return {
    m: reciprocal,
    b: b
  };
}


// line intercept math by Paul Bourke http://paulbourke.net/geometry/pointlineplane/
// Determine the intersection point of two line segments
// Return FALSE if the lines don't intersect
function intersect(p1, p2, p3, p4) {
  let x1 = p1.x,
    y1 = p1.y,
    x2 = p2.x,
    y2 = p2.y,
    x3 = p3.x,
    y3 = p3.y,
    x4 = p4.x,
    y4 = p4.y;

  // Check if none of the lines are of length 0
  if ((x1 === x2 && y1 === y2) || (x3 === x4 && y3 === y4)) {
    return false
  }

  denominator = ((y4 - y3) * (x2 - x1) - (x4 - x3) * (y2 - y1))

  // Lines are parallel
  if (denominator === 0) {
    return false
  }

  let ua = ((x4 - x3) * (y1 - y3) - (y4 - y3) * (x1 - x3)) / denominator
  let ub = ((x2 - x1) * (y1 - y3) - (y2 - y1) * (x1 - x3)) / denominator

  /* this part can be omitted becasue we assume that the lines expand to infinity */

  // // is the intersection along the segments
  // if (ua < 0 || ua > 1 || ub < 0 || ub > 1) {
  // 	return false
  // }

  // Return a object with the x and y coordinates of the intersection
  let x = x1 + ua * (x2 - x1)
  let y = y1 + ua * (y2 - y1)

  return {
    x,
    y
  }
}


/*
 * Smallest enclosing circle - Library (compiled from TypeScript)
 *
 * Copyright (c) 2022 Project Nayuki
 * https://www.nayuki.io/page/smallest-enclosing-circle
 *
 * This program is free software: you can redistribute it and/or modify
 * it under the terms of the GNU Lesser General Public License as published by
 * the Free Software Foundation, either version 3 of the License, or
 * (at your option) any later version.
 *
 * This program is distributed in the hope that it will be useful,
 * but WITHOUT ANY WARRANTY; without even the implied warranty of
 * MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
 * GNU Lesser General Public License for more details.
 *
 * You should have received a copy of the GNU Lesser General Public License
 * along with this program (see COPYING.txt and COPYING.LESSER.txt).
 * If not, see <http://www.gnu.org/licenses/>.
 */
"use strict";
var Point = /** @class */ (function () {
    function Point(x, y) {
        this.x = x;
        this.y = y;
    }
    return Point;
}());
var Circle = /** @class */ (function () {
    function Circle(x, y, r) {
        this.x = x;
        this.y = y;
        this.r = r;
    }
    return Circle;
}());
/*
 * Returns the smallest circle that encloses all the given points. Runs in expected O(n) time, randomized.
 * Note: If 0 points are given, null is returned. If 1 point is given, a circle of radius 0 is returned.
 */
// Initially: No boundary points known
function makeCircle(points) {
    // Clone list to preserve the caller's data, do Durstenfeld shuffle
    var shuffled = points.slice();
    for (var i = points.length - 1; i >= 0; i--) {
        var j = Math.floor(Math.random() * (i + 1));
        j = Math.max(Math.min(j, i), 0);
        var temp = shuffled[i];
        shuffled[i] = shuffled[j];
        shuffled[j] = temp;
    }
    // Progressively add points to circle or recompute circle
    var c = null;
    shuffled.forEach(function (p, i) {
        if (c === null || !isInCircle(c, p))
            c = makeCircleOnePoint(shuffled.slice(0, i + 1), p);
    });

    return c;
}
// One boundary point known
function makeCircleOnePoint(points, p) {
    var c = new Circle(p.x, p.y, 0);
    points.forEach(function (q, i) {
        if (!isInCircle(c, q)) {
            if (c.r == 0)
                c = makeDiameter(p, q);
            else
                c = makeCircleTwoPoints(points.slice(0, i + 1), p, q);
        }
    });
    return c;
}
// Two boundary points known
function makeCircleTwoPoints(points, p, q) {
    var circ = makeDiameter(p, q);
    var left = null;
    var right = null;
    // For each point not in the two-point circle
    for (var _i = 0, points_1 = points; _i < points_1.length; _i++) {
        var r = points_1[_i];
        if (isInCircle(circ, r))
            continue;
        // Form a circumcircle and classify it on left or right side
        var cross = crossProduct(p.x, p.y, q.x, q.y, r.x, r.y);
        var c = makeCircumcircle(p, q, r);
        if (c === null)
            continue;
        else if (cross > 0 && (left === null || crossProduct(p.x, p.y, q.x, q.y, c.x, c.y) > crossProduct(p.x, p.y, q.x, q.y, left.x, left.y)))
            left = c;
        else if (cross < 0 && (right === null || crossProduct(p.x, p.y, q.x, q.y, c.x, c.y) < crossProduct(p.x, p.y, q.x, q.y, right.x, right.y)))
            right = c;
    }
    // Select which circle to return
    if (left === null && right === null)
        return circ;
    else if (left === null && right !== null)
        return right;
    else if (left !== null && right === null)
        return left;
    else if (left !== null && right !== null)
        return left.r <= right.r ? left : right;
    else
        throw new Error("Assertion error");
}
function makeDiameter(a, b) {
    var cx = (a.x + b.x) / 2;
    var cy = (a.y + b.y) / 2;
    var r0 = distance(cx, cy, a.x, a.y);
    var r1 = distance(cx, cy, b.x, b.y);
    return new Circle(cx, cy, Math.max(r0, r1));
}
function makeCircumcircle(a, b, c) {
    // Mathematical algorithm from Wikipedia: Circumscribed circle
    var ox = (Math.min(a.x, b.x, c.x) + Math.max(a.x, b.x, c.x)) / 2;
    var oy = (Math.min(a.y, b.y, c.y) + Math.max(a.y, b.y, c.y)) / 2;
    var ax = a.x - ox;
    var ay = a.y - oy;
    var bx = b.x - ox;
    var by = b.y - oy;
    var cx = c.x - ox;
    var cy = c.y - oy;
    var d = (ax * (by - cy) + bx * (cy - ay) + cx * (ay - by)) * 2;
    if (d == 0)
        return null;
    var x = ox + ((ax * ax + ay * ay) * (by - cy) + (bx * bx + by * by) * (cy - ay) + (cx * cx + cy * cy) * (ay - by)) / d;
    var y = oy + ((ax * ax + ay * ay) * (cx - bx) + (bx * bx + by * by) * (ax - cx) + (cx * cx + cy * cy) * (bx - ax)) / d;
    var ra = distance(x, y, a.x, a.y);
    var rb = distance(x, y, b.x, b.y);
    var rc = distance(x, y, c.x, c.y);
    return new Circle(x, y, Math.max(ra, rb, rc));
}
/* Simple mathematical functions */
var MULTIPLICATIVE_EPSILON = 1 + 1e-14;
function isInCircle(c, p) {
    return c !== null && distance(p.x, p.y, c.x, c.y) <= c.r * MULTIPLICATIVE_EPSILON;
}
// Returns twice the signed area of the triangle defined by (x0, y0), (x1, y1), (x2, y2).
function crossProduct(x0, y0, x1, y1, x2, y2) {
    return (x1 - x0) * (y2 - y0) - (y1 - y0) * (x2 - x0);
}
function distance(x0, y0, x1, y1) {
    return Math.hypot(x0 - x1, y0 - y1);
}
if (!("hypot" in Math)) // Polyfill
    Math.hypot = function (x, y) { return Math.sqrt(x * x + y * y); };


var Vertex = function(x, y) {
  this.x = x
  this.y = y
  this.equals = function(vertex){ return this.x === vertex.x && this.y == vertex.y }
}

var Edge = function(v0, v1) {
  this.v0 = v0
  this.v1 = v1
  this.equals = function(edge){
    return (this.v0.equals(edge.v0) && this.v1.equals(edge.v1)) ||
      (this.v0.equals(edge.v1) && this.v1.equals(edge.v0));
  }
}

// Triangle
var Triangle = function(v0, v1, v2) {
  this.v0 = v0
  this.v1 = v1
  this.v2 = v2
  this.circumCirc = calcCircumCirc(v0,v1,v2)
  this.inCircumcircle = function(v) {
    var dx = this.circumCirc.c.x - v.x;
    var dy = this.circumCirc.c.y - v.y;
    return Math.sqrt(dx * dx + dy * dy) <= this.circumCirc.r;
  }
};

// Remove duplicate edges
var uniqueEdges = function(edges) {
  var uniqueEdges = [];
  for (var i = 0; i < edges.length; ++i) {
    var isUnique = true;

    // See if edge is unique
    for (var j = 0; j < edges.length; ++j) {
      if (i != j && edges[i].equals(edges[j])) {
        isUnique = false;
        break;
      }
    }

    // Edge is unique, add to unique edges array
    isUnique && uniqueEdges.push(edges[i]);
  }

  return uniqueEdges;
};

// Update array of triangles by adding a new vertex
var addVertex = function(vertex, triangles) {
  var edges = [];

  // Remove triangles with circumcircles containing the vertex
  triangles = triangles.filter(function(triangle) {
    if (triangle.inCircumcircle(vertex)) {
      edges.push(new Edge(triangle.v0, triangle.v1));
      edges.push(new Edge(triangle.v1, triangle.v2));
      edges.push(new Edge(triangle.v2, triangle.v0));
      return false;
    }
    return true;
  });


  // Get unique edges
  edges = uniqueEdges(edges);

  // Create new triangles from the unique edges and new vertex
  edges.forEach(function(edge) {
    triangles.push(new Triangle(edge.v0, edge.v1, vertex));
  });


  return triangles;
};


function triangulate(vertices) {
  // Create bounding 'super' triangle
  var st = getBounding2(e) //superTriangle(vertices);

  // Initialize triangles while adding bounding triangle
  var triangles = [st];

  for(let n = 0; n < vertices.length; n++){
    triangles = addVertex(vertices[n], triangles);
  }

  //Remove triangles that share edges with super triangle
  triangles = triangles.filter(function(triangle) {
    return !(triangle.v0 == st.v0 || triangle.v0 == st.v1 || triangle.v0 == st.v2 ||
      triangle.v1 == st.v0 || triangle.v1 == st.v1 || triangle.v1 == st.v2 ||
      triangle.v2 == st.v0 || triangle.v2 == st.v1 || triangle.v2 == st.v2);
  });

  return triangles;
};

function setup() {
  frameRate(60)
  //randomSeed(1000)
  w = min(windowWidth, windowHeight)
  wx = w
  wy = w
  createCanvas(wx, wy)

  pad = w/12

  background(0)
  stroke(255)
  noFill()
  strokeWeight(5)
  strokeJoin(ROUND)

  //randomSeed(1000)
  pointList = []
  pointList2 = []
  for (let n = 0; n < 300; n++) {

    r = w*2 * sqrt(random())
    theta = random() * 2 * PI

    randX = random(pad, w-pad) //w/2 + r*cos(theta)
    randY = random(pad, w-pad) //w/2 + r*sin(theta)

    pointList.push(
      new Vertex(randX, randY)
    )
    pointList2.push(
      new Point(randX, randY)
    )

  }

  strokeWeight(1)
  e = makeCircle(pointList)
}

function draw(){
  background(0)
  strokeWeight(5)
  for(let j = 0; j < pointList.length; j++){
    point(pointList[j].x, pointList[j].y)
  }

  triangles = triangulate(pointList)

  strokeWeight(1)
  stroke(255)
  triangles.forEach(
    (tri, i) => {

      beginShape()
      vertex(tri.v0.x, tri.v0.y)
      vertex(tri.v1.x, tri.v1.y)
      vertex(tri.v2.x, tri.v2.y)

      endShape(CLOSE)
    }
  )

  for(let j = 0; j < pointList.length; j++){
    point(pointList[j].x, pointList[j].y)

  }

noLoop()
}
</script>
<p></p>

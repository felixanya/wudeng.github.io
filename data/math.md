



dot product:

![](http://chart.googleapis.com/chart?cht=tx&chl=\vec{u}\cdot%20\vec{v}=|\vec{u}|\cdot%20|\vec{v}|\cdot%20cos(\theta))

vector3.Cross, direction: left hand rule

perp dot product: 2d version of cross product,
    |u x v| = |u| * |v| * sin(t)
    u is replace by the perpendicular vector rotated 90 to the left
    cp = dx1 * dy2 - dx2 * dy1
    cp is actually the signed magnitude of the classic 3d cross product of vectors (dx1, dy1, 0) and (dx2, dy2, 0),
    obviously the value is simply a scalar product, in which one of the vectors war replaced by its perpendicular.

    dot product of (-y1, x1) and (x2, y2), and (-y1, x1) is 90 rotated of (x1, y1),

    if cp > 0, then the shortest radial sweep from (dx1, dy1) to (dx2, dy2) goes counter-clockwise,
    negative cp means clockwise sweep. zero in cp indicates collinear vectors.

```
// counterclockwise
int ccw (Point P0, Point P1, Point P2) {
    dx1 = P1.x - P0.x;
    dx2 = P2.x - P0.x;
    dy1 = P1.y - P0.y;
    dy2 = P1.y - P0.y;

    if (dy1 * dx2 > dy2 * dx1) return -1;
    if (dx1 * dy2 > dy1 * dx2) return 1;
    if ((dx1 * dx2 < 0) || (dy1 * dy2 < 0)) return 1;
    if ((dx1 * dx1 + dy1 * dy1) < (dx2 * dx2 + dy2 * dy2)) return -1;
    return 0;
}

// segment intersect
bool intersect (Vector2D l1, Vector2D l2) {
    return (((ccw(l1.start, l1.end, l2.start) * ccw(l1.start, l1.end, l2.end)) <= 0)
    && ((ccw(l2.start, l2.end, l1.start) * ccw(l2.start, l2.end, l1.start)) <= 0))
}

```

point is in triangle:
    https://stackoverflow.com/questions/2049582/how-to-determine-if-a-point-is-in-a-2d-triangle
    http://blackpawn.com/texts/pointinpoly/

```
func sign(p1, p2, p3 Point3) float32 {
	return (p1.X-p3.X)*(p2.Y-p3.Y) - (p2.X-p3.X)*(p1.Y-p3.Y)
}

func inside(pt, v1, v2, v3 Point3) bool {
	b1 := sign(pt, v1, v2) <= 0
	b2 := sign(pt, v2, v3) <= 0
	b3 := sign(pt, v3, v1) <= 0
	return ((b1 == b2) && (b2 == b3))
}
```

直线 ax + by + c = 0，法向量为[a, b]
证明：[a, b] 垂直于直线上的任意两点构成的向量。

对于平面：ax + by + cz = d, 法向量为 [a, b, c]


https://stackoverflow.com/questions/26315401/explanation-of-ccw-algorithm
navmesh

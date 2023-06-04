# Intersection Points ▶️

### Contents
 - [Intersection with a Line](#intersection-with-a-line)
 - [Intersection with a Shape](#intersection-with-a-shape)
 - [Line Intersection with a Plane](#line-intersection-with-a-plane)
 - [Line Intersection with a Sphere](#line-intersection-with-a-sphere)

This blog post details my exploration into computing intersection points with lines and shapes.

### Intersection with a Line

The intersection point between two lines can be found using the formula in the image below. This formula takes the x and y values from the two lines (4 points total). This formula and examples of its implementation can be found here ([formula](https://dirask.com/posts/JavaScript-calculate-intersection-point-of-two-lines-for-given-4-points-VjvnAj), [implementation](https://www.habrador.com/tutorials/math/5-line-line-intersection/))

![image](https://github.com/markom9822/markom9822.github.io/assets/96113848/d756c9b0-a75b-40e4-ac7b-8bd81ae94c3c)

This formula is implemented into a method shown below. This method takes 4 vectors, the start and end for each line. Firstly the x and y values for each vector are computed. The coordinates of the intersection are then calculated (Px, Py) forming the intersection vector. Before we return this vector we need to check if this intersection point is within the bounds of both lines. If this passes then the vector is returned otherwise a default value is returned.

```cs
/// <summary>
/// Find the intersection point between two lines.
/// </summary>
/// <param name="line1Start"> start vector for line 1</param>
/// <param name="line1End"> end vector for line 1</param>
/// <param name="line2Start"> start vector for line 2</param>
/// <param name="line2End"> end vector for line 2</param>
/// <returns> intersection point vector</returns>
public Vector3 IntersectionPointTwoLines(Vector3 line1Start, Vector3 line1End, Vector3 line2Start, Vector3 line2End)
{
    (float x1, float y1) = GetXYPosition(line1Start);
    (float x2, float y2) = GetXYPosition(line1End);
    (float x3, float y3) = GetXYPosition(line2Start);
    (float x4, float y4) = GetXYPosition(line2End);
        
    float topX = (x1 * y2 - x2 * y1) * (x3 - x4) - (x3 * y4 - x4 * y3) * (x1 - x2);
    float topY = (x1 * y2 - x2 * y1) * (y3 - y4) - (x3 * y4 - x4 * y3) * (y1 - y2);
    float bottom = (x1 - x2) * (y3 - y4) - (y1 - y2) * (x3 - x4);

    float pX = topX/bottom;
    float pY = topY/bottom;

    Vector3 pVector = new Vector3(pX, pY, 0f);

    bool isInBoundsLine1 = IsIntersectionInBounds(line1Start, line1End, pVector);
    bool isInBoundsLine2 = IsIntersectionInBounds(line2Start, line2End, pVector);

    if (!isInBoundsLine1|| !isInBoundsLine2)
    {
        return default;
    }

    return pVector;
}
```

This method below simply just returns the x and y values for the given vector.

```cs
/// <summary>
/// Returns the x and y position of the vector.
/// </summary>
/// <param name="vector"></param>
/// <returns> x and y values</returns>
public (float, float) GetXYPosition(Vector3 vector)
{
    return (vector.x, vector.y);
}
```

The method below checks if the intersection point is between the given start and end vectors. This is done by comparing the distance between the intersection point and either end and the distance between the start and end points. The method returns true if within the bounds and false if not.

```cs
/// <summary>
/// Checks if intersection point is between line points.
/// </summary>
/// <param name="lineStart"> line start vector</param>
/// <param name="lineEnd"> line end vector</param>
/// <param name="intersection"> intersection point vector</param>
/// <returns> true if within, false if not</returns>
public bool IsIntersectionInBounds(Vector3 lineStart, Vector3 lineEnd, Vector3 intersection)
{
    float distAC = Vector3.Distance(lineStart, intersection);
    float distBC = Vector3.Distance(lineEnd, intersection);
    float distAB = Vector3.Distance(lineStart, lineEnd);

    if (Math.Abs(distAC + distBC - distAB) > 0.001f)
    {
        return false;
    }
    return true;
}
```

A demo of this method in action can be seen below with some gizmos for visualisation. The red sphere shows the intersection point between the two lines. When the lines are not intersecting this sphere is no longer visible.

![Intersect Line Demo 1](https://github.com/markom9822/markom9822.github.io/assets/96113848/1369a8d7-2bde-4bc7-86d8-7da73acf3712)

### Intersection with a Shape

The method described above can be used to detect intersections with multiple lines. Here multiple lines are used to make a shape. Again the red spheres show where the intersection points are. The number of points in the shape can be adjusted and the number of intersections follow. See a demo of this in the gif below.

![Intersect Shapes Demo 1](https://github.com/markom9822/markom9822.github.io/assets/96113848/f0c7332a-2ca1-4f94-a42b-2a809fe5f213)

### Line Intersection with a Plane



### Line Intersection with a Sphere

In order to compute intersection points between a ray and a sphere we can take a geometrical approach. The figure below shows a ray starting at $p$ and pointed in direction $R$. This ray has two intersection points on the sphere, $p_0$ and $p_1$.
We can get $p_c$ simply subtracting the ray position from the sphere center position.
$$p_c = c - p$$
Using this we can get $d_p$ using $R$ and the direction of $p_c$ and computing their dot product.
$$d_p = pc \cdot R$$
Then using the Pythagorean theorem we can calculate $b$.
$$b = \sqrt{p_c \cdot p_c - d_p^2}$$
We can get the distances from the ray origin point by adding and subtracting $d_c$ from $d_p$ to get $d_0$ and $d_1$.
$$d_0 = d_p + d_c$$
$$d_1 = d_p - d_c$$
From this we can move along the direction of the ray by distance $d_0$ to get $p_0$ and the same with $d_1$ and $p_1$.
We can also say that if $d_0$ is negative then $p_0$ no longer exists and the same with $d_1$ and $p_1$.

![image](https://github.com/markom9822/markom9822.github.io/assets/96113848/ec5ac356-336b-45f5-bfca-72b918b353e7)

[Source: [scratchapixel.com](https://en.wikipedia.org/wiki/B%C3%A9zier_curve)]

Implementing this into code is quite simple with the use of `Vector3.Dot` and `Mathf.Sqrt`. This is shown in the method below.

```cs
/// <summary>
/// Computes intersection points between ray and sphere.
/// </summary>
/// <param name="sphereCenter"> center position of sphere</param>
/// <param name="sphereRadius"> radius of sphere</param>
/// <param name="rayPosition"> position of where ray is projected from</param>
/// <param name="rayDirection"> direction of ray projection</param>
/// <returns> returns two intersection point positions, default if point does not exist</returns>
public (Vector3, Vector3) RaySphereIntersection(Vector3 sphereCenter, float sphereRadius, Vector3 rayPosition, Vector3 rayDirection)
{
    Vector3 rayPointToCentre = sphereCenter - rayPosition;
    float pointDotDirection = Vector3.Dot(rayPointToCentre, rayDirection);
    float perpDist = Mathf.Sqrt(Vector3.Dot(rayPointToCentre, rayPointToCentre) - pointDotDirection * pointDotDirection);
    float intersectDist = Mathf.Sqrt(sphereRadius * sphereRadius - perpDist * perpDist);

    float intersectPointDist0 = pointDotDirection - intersectDist;
    float intersectPointDist1 = pointDotDirection + intersectDist;
        
    Vector3 intersectionPoint1 = rayPosition + (rayDirection.normalized * intersectPointDist0);
    Vector3 intersectionPoint2 = rayPosition + (rayDirection.normalized * intersectPointDist1);

    if (intersectPointDist0 < 0) intersectionPoint1 = default;
    if (intersectPointDist1 < 0) intersectionPoint2 = default;

    return (intersectionPoint1, intersectionPoint2);
}
```


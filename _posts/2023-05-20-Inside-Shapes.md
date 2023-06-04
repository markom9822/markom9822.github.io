# Inside Shapes 🎯

### Contents
 - [Inside Polygon](#inside-polygon)
 - [Inside Cuboid](#inside-cuboid)
 - [Inside Sphere](#inside-sphere)

This blog post details my exploration into computing whether a point is inside a shape.

### Inside Polygon

A well known problem in computer graphics is the point-in-polygon (PIP) problem. This asks whether a given point in the plane lies inside, outside, or on the boundary of a polygon. A simple and common approach to this is called the ray cast algorithm. 
This algorithm computes how many times a ray, starting from the point and going in any fixed direction, intersects the edges of the polygon. If the ray intersects with an even number of edges or zero edges then it is outside the polygon. If the point is on the inside of the polygon then it will intersect with an odd number of edges. 

![image](https://github.com/markom9822/markom9822.github.io/assets/96113848/4f319bf3-7344-4e1d-bd63-cb90c78eea66)

For more info see this [wiki article](https://en.wikipedia.org/wiki/Point_in_polygon). This algorithm is implemented in the method shown below.
This method loops through the points in the shape, checks for intersections with the ray and puts them into a list. Double counting at corners is checked and corrected. True is returned if the point is inside the polygon and false if not.

```cs
/// <summary>
/// Checks if target point is inside shape.
/// </summary>
/// <param name="pointsList"> list of points in shape</param>
/// <param name="targetTransform"> target point transform</param>
/// <returns> true if inside the shape, false if not</returns>
public bool IsInsideShape(List<Transform> pointsList, Transform targetTransform)
{
    List<Vector3> intersectionsList = new List<Vector3>();
    intersectionsList.Clear();

    for (int i = 0; i < pointsList.Count; i++)
    {
        if (i == pointsList.Count-1)
        {
            AddIntersectionPoint(pointsList[i].localPosition, pointsList[0].localPosition, targetTransform, intersectionsList);
            break;
        }
        AddIntersectionPoint(pointsList[i].localPosition,pointsList[i+1].localPosition, targetTransform, intersectionsList);
    }

    // check for double counting at corners
    for (int i = 0; i < intersectionsList.Count - 1; i++)
    {
        for (int j = i + 1; j < intersectionsList.Count; j++)
        {
            if (intersectionsList[i] == intersectionsList[j])
            {
                intersectionsList.Remove(intersectionsList[j]);
            }
        }
    }
        
    if (intersectionsList.Count == 0 || intersectionsList.Count % 2 == 0)
    {
        // outside box
        return false;
    }
    // inside box
    return true;
}    
```
The method below looks for an intersection point with the ray pointing in the **right** direction and if so it is added to the list.

```cs
/// <summary>
/// Adds intersection point to list
/// </summary>
/// <param name="lineStart"> line start vector</param>
/// <param name="lineEnd"> line end vector</param>
/// <param name="targetTransform"> target point transform</param>
/// <param name="pointList"> list of shape points</param>
public void AddIntersectionPoint(Vector3 lineStart, Vector3 lineEnd, Transform targetTransform,  List<Vector3> pointList)
{
    Vector3 interPoint = IntersectionPointTwoLines(lineStart, lineEnd,
        targetTransform.localPosition, targetTransform.localPosition + targetTransform.right);
        
    Vector3 interPointLocal = transform.TransformPoint(interPoint);
    if (interPoint != default)
    {
        pointList.Add(interPointLocal);
    }
}
```

The intersection point method from a previous blog post was used here with slight modifications to allow intersection detection with a ray instead of a line.

```cs
/// <summary>
/// Find the intersection point between two lines.
/// Adjusted for the inside shape method.
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

    if (!isInBoundsLine1 || pVector.x < line2Start.x)
    { 
        return default;
    }

    return pVector;
}
```

A demo of this algorithm in action can be seen in the gif below. The number of intersections points is shown, the ray and intersection points are shown in red, the target point is a green sphere when inside the polygon and red when outside.

![Inside Shape Demo 1](https://github.com/markom9822/markom9822.github.io/assets/96113848/6df23fbc-0471-4d97-8565-96c14d7a2c0f)

### Inside Cuboid

The method shown below returns whether a point is inside of a cuboid or not. The maths used to compute this uses four key points on the cuboid (P1, P2, P4 and P5) as shown in the image below.

![image](https://github.com/markom9822/markom9822.github.io/assets/96113848/5ff98d12-0e36-4a97-aee8-415366142807)

The i, j and k vectors are computed as shown below. The v vector is from the point of interest to the corner (P1).

![image](https://github.com/markom9822/markom9822.github.io/assets/96113848/11642898-f142-4a9a-8dee-3c5edb88b080)

If the following conditions are satisfied then we can say that the point is inside the cuboid.

![image](https://github.com/markom9822/markom9822.github.io/assets/96113848/e23c1031-9e85-40e6-8390-113a8ed8fbd4)

```cs
/// <summary>
/// Checks if target point is inside cuboid.
/// </summary>
/// <param name="cuboidCentre"> centre of the cuboid</param>
/// <param name="cuboidSize"> cuboid size vector</param>
/// <param name="targetPosition"> target point transform</param>
/// <returns></returns>
public bool IsInsideCuboid(Transform cuboidCentre, Vector3 cuboidSize, Vector3 targetPosition)
{
    Vector3 cubeCentreLocalPos = cuboidCentre.localPosition;
    Vector3 P1 = new Vector3(cubeCentreLocalPos.x - cuboidSize.x / 2, cubeCentreLocalPos.y - cuboidSize.y / 2, cubeCentreLocalPos.z - cuboidSize.z / 2);
    Vector3 P1Local = cuboidCentre.TransformPoint(P1);

    Vector3 P2 = new Vector3(cubeCentreLocalPos.x - cuboidSize.x / 2, cubeCentreLocalPos.y - cuboidSize.y / 2, cubeCentreLocalPos.z + cuboidSize.z / 2);
    Vector3 P2Local = cuboidCentre.TransformPoint(P2);

    Vector3 P4 = new Vector3(cubeCentreLocalPos.x + cuboidSize.x / 2, cubeCentreLocalPos.y - cuboidSize.y / 2, cubeCentreLocalPos.z - cuboidSize.z / 2);
    Vector3 P4Local = cuboidCentre.TransformPoint(P4);

    Vector3 P5 = new Vector3(cubeCentreLocalPos.x - cuboidSize.x / 2, cubeCentreLocalPos.y + cuboidSize.y / 2, cubeCentreLocalPos.z - cuboidSize.z / 2);
    Vector3 P5Local = cuboidCentre.TransformPoint(P5);

    Vector3 i = P2Local - P1Local;
    Vector3 j = P4Local - P1Local;
    Vector3 k = P5Local - P1Local;
    Vector3 v = targetPosition - P1Local;

    float vdoti = Vector3.Dot(v, i);
    float vdotj = Vector3.Dot(v, j);
    float vdotk = Vector3.Dot(v, k);

    float idoti = Vector3.Dot(i, i);
    float jdotj = Vector3.Dot(j, j);
    float kdotk = Vector3.Dot(k, k);

    if (vdoti > 0 && vdoti < idoti && vdotj > 0 && vdotj < jdotj && vdotk > 0 && vdotk < kdotk)
    {
        // inside cuboid
        return true;
    }
    // outside cuboid
    return false;
}
```

The gif below shows a demo of this method in action. The black gizmo shows the cuboid shape and the point of interest is moved in and out of the cuboid. If the point is inside it is green otherwise it is red.

![Inside Cuboid Demo 1](https://github.com/markom9822/markom9822.github.io/assets/96113848/b0f7b382-f7e0-48e3-8d57-7267db31cb02)


### Inside Sphere


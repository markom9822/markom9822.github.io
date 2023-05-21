# Inside Shapes 🎯

### Contents
 - [Inside Polygon](#inside-polygon)

This blog post details my exploration into computing intersection points with lines and shapes.

### Inside Polygon

Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

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

Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.

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

Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.


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

Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.

![Inside Shape Demo 1](https://github.com/markom9822/markom9822.github.io/assets/96113848/6df23fbc-0471-4d97-8565-96c14d7a2c0f)



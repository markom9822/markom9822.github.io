# Intersection Points ▶️

### Contents
 - [Intersection with a Line](#intersection-with-a-line)
 - [Intersection with a Shape](#intersection-with-a-shape)

This blog post details my exploration into computing intersection points with lines and shapes.

### Intersection with a Line

Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.

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

Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo conse

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

Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.

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

Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.Lorem

![Intersect Line Demo 1](https://github.com/markom9822/markom9822.github.io/assets/96113848/1369a8d7-2bde-4bc7-86d8-7da73acf3712)

Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.Lorem

### Intersection with a Shape

Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.


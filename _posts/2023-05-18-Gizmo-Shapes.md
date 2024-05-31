# Gizmo Shapes 🟠 🔺 🟩

### Contents
 - [Bézier Curves](#bézier-curves)
 - [Waves](#waves)
 - [Boxes](#boxes)
 - [Circles](#circles)
 - [Ellipses](#ellipses) 
 - [Sectors](#sectors)
 - [Spirals](#spirals)

This blog post details my exploration into generating shapes and using gizmos to visualise them.

### Bézier Curves
A Bézier curve is a parametric curve and has a set of discrete "control points" which define a smooth and continuous curve. This is done by means of a formula. There are several orders of Bézier curves (Linear, Quadratic, Cubic etc.). For more info see this wiki article ([Bézier Curves wiki](https://en.wikipedia.org/wiki/B%C3%A9zier_curve)).

![Bézier_3_big](https://github.com/markom9822/markom9822.github.io/assets/96113848/a2c4d41e-e949-4d57-858f-3057844be232)

#### Quadratic Bézier Curve Equation:
<img src="https://github.com/markom9822/markom9822.github.io/assets/96113848/898538ac-b9f3-4b1f-af8e-4087a2ed2bf8" width="400">

#### Cubic Bézier Curve Equation:
<img src="https://github.com/markom9822/markom9822.github.io/assets/96113848/10cb2f34-043a-468e-b2c9-9daadd7ac4d7" width="500">

Using the cubic bezier equation we can create the following method in C#. This method takes in a float `t` which represents the progress (0-1) along the curve and returns point at that position in space.

```cs
private Vector3 CubicBezierPoint(float t)
{
   // Eqn: B(t) = (1-t)^3 P_0 + 3(1-t)^2 t*P_1 + 3(1-t)t^2 P_2 + t^3 P_3, t[0,1]
   float omt = 1.0f - t;
   return m_P0.position * (omt * omt * omt) + m_P1.position * (3 * omt * omt * t) +
          m_P2.position * (3 * omt * t * t) + m_P3.position * (t * t * t);
}
```
We can also do the same thing for a tetra bezier curve like this:

```cs
private Vector3 TetraBezierPoint(float t)
{
   // Eqn: B(t) = (1-t)^4 P_0 + 4(1-t)^3 t*P_1 + 6(1-t)^2t^2 P_2 + 4(1-t)^3t^2 P_3 + t^4 P_4, t[0,1]
   float omt = 1.0f - t;
   return m_P0.position * (omt * omt * omt * omt) + m_P1.position * (4 * omt * omt * omt * t) +
          m_P2.position * (6 * omt * omt * t * t) + m_P3.position * (4 * omt * t * t * t)  + m_P4.position * (t * t * t * t);
}
```
Now in order to create our curve we need to draw a line for each of the curve segments. When the number of segments is high enough we get a nice smooth looking curve.

```cs
/// <summary>
/// Generates line points for Cubic Bezier Curve.
/// </summary>
/// <param name="curveSegments"> number of segments in the curve</param>
/// <returns></returns>
public Vector3[] GenerateBezierLinePoints(int curveSegments)
{
    Vector3[] linePointArray = new Vector3[curveSegments + 1];
    curveSegments = Mathf.Max(1, curveSegments);
    
    for (int i = 0; i <= curveSegments; i++)
    {
        float t = i / (float)curveSegments;
        linePointArray[i] = CubicBezierPoint(t);
     }
    return linePointArray;
}
```
This gif shows black spheres for each of the segment points along the curve with the control points in teal. Moving the control points will affect the shape of the curve.

![Bezier Curve Demo 1](https://github.com/markom9822/markom9822.github.io/assets/96113848/6383859b-1b23-4735-9254-66eba189db2f)

We can adjust the number of segments to control curve smoothness. 

![Bezier Curve Demo 2](https://github.com/markom9822/markom9822.github.io/assets/96113848/b0cd2538-4c2c-4be5-95b8-1ce70cf44990)

I also added in the option to change the curve from cubic to tetric. You can now see we have an extra control point which we can use to get more interesting shapes.

![Bezier Curve Demo 3](https://github.com/markom9822/markom9822.github.io/assets/96113848/ba5a1e8c-e0ca-4c55-b35b-5421cf173c8d)

### Waves
We can create waveforms simply by using the sin, cos and tan functions.
Here I created a script to be able to create sin, cos or tan wave gizmos from a start point to an end point. The number of segments can be adjusted with just like the bezier curve examples above.
You can also adjust the wave amplitude and wavelength.

```cs
/// <summary>
/// Generates line points for given wave type.
/// </summary>
/// <param name="startPoint"> wave start point</param>
/// <param name="endPoint"> wave end point</param>
/// <param name="amplitude"> amplitude of the wave</param>
/// <param name="waveSegments"> number of segments in the wave</param>
/// <param name="wavelength"> wave wavelength</param>
/// <param name="waveType"> type of wave</param>
/// <returns></returns>
public Vector3[] GenerateWaveLinePoints(Transform startPoint, Transform endPoint, float amplitude, int waveSegments, float wavelength, WaveType waveType)
{
    Vector3[] linePointArray = new Vector3[waveSegments + 1];
    waveSegments = Mathf.Max(1, waveSegments);

    float k = 2 * Mathf.PI / wavelength;
    float distx = endPoint.position.x - startPoint.position.x;
    float disty = endPoint.position.y - startPoint.position.y;
    float distz = endPoint.position.z - startPoint.position.z;
    float yChange = 0f;

    for (int i = 0; i <= waveSegments; i++)
    {
        float x = i * (distx / waveSegments);
        float y = i * (disty / waveSegments);
        float z = i * (distz / waveSegments);

        Vector3 vec = new Vector3(x, 0, z);
        yChange = waveType switch
        {
            WaveType.Sine => amplitude * Mathf.Sin(k * vec.magnitude) + y,
            WaveType.Cosine => amplitude * Mathf.Cos(k * vec.magnitude) + y,
            WaveType.Atan => amplitude * Mathf.Atan(k * vec.magnitude) + y,
            _ => yChange
        };
            
        linePointArray[i] = new Vector3(x , yChange, z) + startPoint.position;
    }
    return linePointArray;
}
```

![Wave Demo 1](https://github.com/markom9822/markom9822.github.io/assets/96113848/b9ba8ef1-c08d-4807-a0ac-3772ba897753)

The wave points are also local to the start and end points. Using this the direction and length of the wave can be adjusted to your liking.

### Boxes
Here I explore making 2D box gizmos of various different shapes by playing with the number of segments in the box.
The simple method below takes a center origin, distance from the center and a number of segments.

```cs
/// <summary>
/// Generates line points for box.
/// </summary>
/// <param name="centre"> centre transform of box</param>
/// <param name="distFromCentre"> distance from the centre point</param>
/// <param name="numSegments"> number of segments in ellipse</param>
public Vector3[] GenerateBoxLinePoints(Transform centre, float distFromCentre, int numSegments)
{
    numSegments = Mathf.Max(1, numSegments);
    Vector3[] linePointArray = new Vector3[numSegments + 1];

    for (int i = 0; i <= numSegments; i++)
    {
        linePointArray[i] = centre.position - Quaternion.Euler(0, 0, 360/numSegments*(i+1)) * centre.up*distFromCentre;
    }

    return linePointArray;
}
```
As you can see in the gif below we can play with the different parameters to get a variety of shapes.

![Box Demo 1](https://github.com/markom9822/markom9822.github.io/assets/96113848/eb86889d-cd14-4549-b66d-86bff60a9421)


### Circles
Here I explore creating 2D circle gizmos using a similar method to the box example above.

```cs
/// <summary>
/// Generates line points for circle.
/// </summary>
/// <param name="pos"> centre point of circle</param>
/// <param name="normal"> normal vector of the circle</param>
/// <param name="radius"> circle radius</param>
/// <param name="numSegments"> number of segments in the circle</param>
public Vector3[] GenerateCircleLinePoints(Vector3 pos, Vector3 normal, float radius, int numSegments)
{
    Vector3[] linePointArray = new Vector3[numSegments];
        
    Vector3 temp = (normal.x < normal.z) ? new Vector3(1f, 0f, 0f) : new Vector3(0f, 0f, 1f);
    Vector3 forward = Vector3.Cross(normal, temp).normalized;
    Vector3 right = Vector3.Cross(forward, normal).normalized;
 
    Vector3 prevPt = pos + (forward * radius);
    float angleStep = (Mathf.PI * 2f) / numSegments;
        
    for (int i = 0; i < numSegments; i++)
    {
        float angle = (i == numSegments - 1) ? 0f : (i + 1) * angleStep;
            
        Vector3 nextPtLocal = new Vector3(Mathf.Sin(angle), 0f, Mathf.Cos(angle)) * radius;
        Vector3 nextPt = pos + (right * nextPtLocal.x) + (forward * nextPtLocal.z);
            
        linePointArray[i] = prevPt;
        prevPt = nextPt;
    }

    return linePointArray;
}
```

We can adjust the circle radius and the number of segments in the curve to control smoothness.

![Circle Demo 1](https://github.com/markom9822/markom9822.github.io/assets/96113848/5f804026-26e8-44b4-9c6b-263a472822d9)


### Ellipses
Here I explore creating 2D ellipse gizmos, this builds on top of the work done on circles by introducing the `A` and `B` parameters.
`A` represents the semi-major axis (half of the length of the major axis) and `B` represents the semi-minor axis (half of the length of the minor axis).

```cs
/// <summary>
/// Generates line points for ellipse.
/// </summary>
/// <param name="pos"> centre point of ellipse</param>
/// <param name="normal"> normal vector of the ellipse</param>
/// <param name="aParam"> A parameter in ellipse equation</param>
/// <param name="bParam"> B parameter in ellipse equation</param>
/// <param name="numSegments"> number of segments in ellipse</param>
public Vector3[] GenerateEllipseLinePoints(Vector3 pos, Vector3 normal, float aParam, float bParam, int numSegments)
{
    Vector3[] linePointArray = new Vector3[numSegments];

    Vector3 temp = (normal.x < normal.z) ? new Vector3(1f, 0f, 0f) : new Vector3(0f, 0f, 1f);
    Vector3 forward = Vector3.Cross(normal, temp).normalized;
    Vector3 right = Vector3.Cross(forward, normal).normalized;
 
    Vector3 prevPt = pos + (forward * aParam);
    float angleStep = (Mathf.PI * 2f) / numSegments;
    for (int i = 0; i < numSegments; i++)
    {
        float angle = (i == numSegments - 1) ? 0f : (i + 1) * angleStep;
            
        Vector3 nextPtLocal = new Vector3(bParam*Mathf.Sin(angle), 0f, aParam*Mathf.Cos(angle));
        Vector3 nextPt = pos + (right * nextPtLocal.x) + (forward * nextPtLocal.z);
            
        linePointArray[i] = prevPt;
        prevPt = nextPt;
    }

    return linePointArray;
}
```
We can adjust the shape of the elipse using `A` an `B` as well as the number of segments in the curve to control smoothness.

![Ellipse Demo 1](https://github.com/markom9822/markom9822.github.io/assets/96113848/26897315-9094-456f-be06-2da2813e48b3)

### Sectors
Here I explore creating a 2D sector gizmo, this involves creating an arc of points which has its own angle and radius.

```cs
/// <summary>
/// Generates line points for arc.
/// </summary>
/// <param name="position"> centre point of the arc</param>
/// <param name="dir"> direction of the arc</param>
/// <param name="anglesRange"> arc angle range</param>
/// <param name="radius"> radius of the arc</param>
/// <param name="numSegments"> number of segments in the circle</param>
public Vector3[] GenerateArcLinePoints(Vector3 position, Vector3 dir, float anglesRange, float radius, int numSegments)
{
    Vector3[] linePointArray = new Vector3[numSegments + 1];
        
    float srcAngles = GetAnglesFromDir(position, dir);
    Vector3 initialPos = position;
    Vector3 posA = initialPos;
    float stepAngles = anglesRange / numSegments;
    float angle = srcAngles - anglesRange / 2;
        
    for (int i = 0; i <= numSegments; i++)
    {
        float rad = Mathf.Deg2Rad * angle;
        Vector3 posB = initialPos;
        posB += new Vector3(radius * Mathf.Cos(rad), 0, radius * Mathf.Sin(rad));
            
        angle += stepAngles;
        linePointArray[i] = posA;
            
        posA = posB;
    }
    return linePointArray;
}
```
This gif below shows the effect of changing angle range, radius and number of segments.

![Arc Demo 1](https://github.com/markom9822/markom9822.github.io/assets/96113848/0cca0059-4e38-4653-8303-430cdba1ba27)

### Spirals
Here I explore creating a 2D sprial gizmo, this builds on top of the work on creating an ellipse.
This time the distance away from the center grows we rotate through the spiral.

```cs
/// <summary>
/// Generates line points for spiral.
/// </summary>
/// <param name="pos"> centre point of spiral</param>
/// <param name="normal"> normal vector of the spiral</param>
/// <param name="aParam"> A parameter in spiral equation</param>
/// <param name="bParam"> B parameter in spiral equation</param>
/// <param name="numSegments"> number of segments in spiral</param>
/// <param name="spiralTurns"> number of turns in the spiral</param>
public Vector3[] GenerateSpiralLinePoints(Vector3 pos, Vector3 normal, float aParam, float bParam, int numSegments, float spiralTurns)
{
    Vector3[] linePointArray = new Vector3[numSegments];

    Vector3 temp = (normal.x < normal.z) ? new Vector3(1f, 0f, 0f) : new Vector3(0f, 0f, 1f);
    Vector3 forward = Vector3.Cross(normal, temp).normalized;
    Vector3 right = Vector3.Cross(forward, normal).normalized;
 
    Vector3 prevPt = pos;
    float angleStep = (Mathf.PI * 2f) / numSegments;
    for (int i = 0; i < numSegments*spiralTurns; i++)
    {
        float angle = (Math.Abs(i - (numSegments*spiralTurns - 1)) < 0.001f) ? 0f : (i + 1) * angleStep;
            
        Vector3 nextPtLocal = new Vector3((aParam*angle)*Mathf.Cos(angle), 0f, (bParam*angle)*Mathf.Sin(angle));
        Vector3 nextPt = pos + (right * nextPtLocal.x) + (forward * nextPtLocal.z);
            
        linePointArray[i] = prevPt;
        prevPt = nextPt;
    }

    return linePointArray;
}
```
This gif shows how we can change the look of the spiral using the `A` and `B` parameters, the number of turns and the number of segments.

![Spiral Demo 1](https://github.com/markom9822/markom9822.github.io/assets/96113848/5684e33e-475f-4893-b513-ca04e7a130e2)

This has been my quick blog on creating custom gizmo shapes in C#. Please see extra resources below if you are interested.

*Extra Resources:*
- Video - [Bezier Curves Explained](https://www.youtube.com/watch?v=pnYccz1Ha34)
- Video - [Moving along Bezier Curve](https://www.youtube.com/watch?v=11ofnLOE8pw)

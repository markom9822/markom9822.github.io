# Gizmo Shapes 🟠 🔺 🟩

### Contents
 - [Bézier Curves](#bézier-curves)
 - [Waves](#waves)
 - [Boxes](#boxes)
 - [Circles](#circles)
 - [Ellipses](#ellipses) 
 - [Sectors](#sectors)

This blog post details my exploration into generating shapes and using gizmos to visualise them.

### Bézier Curves
A Bézier curve is a parametric curve and has a set of discrete "control points" which define a smooth and continuous curve. This is done by means of a formula. There are several orders of Bézier curves (Linear, Quadratic, Cubic etc.). For more info see this wiki article ([Bézier Curves wiki](https://en.wikipedia.org/wiki/B%C3%A9zier_curve)).

![Bézier_3_big](https://github.com/markom9822/markom9822.github.io/assets/96113848/a2c4d41e-e949-4d57-858f-3057844be232)

#### Quadratic Bézier Curve Equation:
<img src="https://github.com/markom9822/markom9822.github.io/assets/96113848/898538ac-b9f3-4b1f-af8e-4087a2ed2bf8" width="400">

#### Cubic Bézier Curve Equation:
<img src="https://github.com/markom9822/markom9822.github.io/assets/96113848/10cb2f34-043a-468e-b2c9-9daadd7ac4d7" width="500">

Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

```csharp
   private Vector3 CubicBezierPoint(float t)
    {
        // Eqn: B(t) = (1-t)^3 P_0 + 3(1-t)^2 t*P_1 + 3(1-t)t^2 P_2 + t^3 P_3, t[0,1]
        float omt = 1.0f - t;
        return m_P0.position * (omt * omt * omt) + m_P1.position * (3 * omt * omt * t) +
               m_P2.position * (3 * omt * t * t) + m_P3.position * (t * t * t);
    }
```
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

```csharp
   private Vector3 TetraBezierPoint(float t)
    {
        // Eqn: B(t) = (1-t)^4 P_0 + 4(1-t)^3 t*P_1 + 6(1-t)^2t^2 P_2 + 4(1-t)^3t^2 P_3 + t^4 P_4, t[0,1]
        float omt = 1.0f - t;
        return m_P0.position * (omt * omt * omt * omt) + m_P1.position * (4 * omt * omt * omt * t) +
               m_P2.position * (6 * omt * omt * t * t) + m_P3.position * (4 * omt * t * t * t)  + m_P4.position * (t * t * t * t);
    }
```
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

```csharp
   /// <summary>
   /// Generates line points for Cubic Bezier Curve.
   /// </summary>
   /// <param name="linePointArray"> array of point positions</param>
   /// <param name="curveSegments"> number of segments in the curve</param>
   /// <returns></returns>
   public Vector3[] GenerateBezierLinePoints(Vector3[] linePointArray, int curveSegments)
   {
       curveSegments = Mathf.Max(1, curveSegments);
       linePointArray = new Vector3[curveSegments + 1];
    
       for (int i = 0; i <= curveSegments; i++)
       {
           float t = i / (float)curveSegments;
    
           linePointArray[i] = CubicBezierPoint(t);
       }
       return linePointArray;
   }
```
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

![Bezier Curve Demo 1](https://github.com/markom9822/markom9822.github.io/assets/96113848/6383859b-1b23-4735-9254-66eba189db2f)

Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

![Bezier Curve Demo 2](https://github.com/markom9822/markom9822.github.io/assets/96113848/b0cd2538-4c2c-4be5-95b8-1ce70cf44990)

Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

![Bezier Curve Demo 3](https://github.com/markom9822/markom9822.github.io/assets/96113848/ba5a1e8c-e0ca-4c55-b35b-5421cf173c8d)

Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

*Extra Resources:*
- Video - [Bezier Curves Explained](https://www.youtube.com/watch?v=pnYccz1Ha34)
- Video - [Moving along Bezier Curve](https://www.youtube.com/watch?v=11ofnLOE8pw)

### Waves
Waveforms can simply be generated using the sin, cos and tan functions.

Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

![Wave Demo 1](https://github.com/markom9822/markom9822.github.io/assets/96113848/b9ba8ef1-c08d-4807-a0ac-3772ba897753)

Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

```csharp
   /// <summary>
   /// Generates line points for given wave type.
   /// </summary>
   /// <param name="linePointArray"> array to store points on wave</param>
   /// <param name="waveSegments"> number of segments in the wave</param>
   /// <param name="startPoint"> wave start point</param>
   /// <param name="endPoint"> wave end point</param>
   /// <param name="amplitude"> amplitude of the wave</param>
   /// <param name="wavelength"> wave wavelength</param>
   /// <param name="waveType"> type of wave</param>
   /// <returns></returns>
   public Vector3[] GenerateWaveLinePoints(Vector3[] linePointArray, int waveSegments, Transform startPoint, Transform endPoint, float amplitude, float wavelength, WaveType waveType)
   {
       waveSegments = Mathf.Max(1, waveSegments);
       linePointArray = new Vector3[waveSegments + 1];

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




### Boxes
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

### Circles
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

```csharp
    /// <summary>
    /// Generates line points for circle.
    /// </summary>
    /// <param name="linePointArray"> array of point positions</param>
    /// <param name="pos"> centre point of circle</param>
    /// <param name="normal"> normal vector of the circle</param>
    /// <param name="numSegments"> number of segments in the circle</param>
    public Vector3[] GenerateCircleLinePoints(Vector3[] linePointArray, Vector3 pos, Vector3 normal, float radius, int numSegments)
    {
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

Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

![Circle Demo 1](https://github.com/markom9822/markom9822.github.io/assets/96113848/5f804026-26e8-44b4-9c6b-263a472822d9)


### Ellipses
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

```csharp
    /// <summary>
    /// Generates line points for ellipse.
    /// </summary>
    /// <param name="linePointArray"> array of point positions</param>
    /// <param name="pos"> centre point of ellipse</param>
    /// <param name="normal"> normal vector of the ellipse</param>
    /// <param name="aParam"> A parameter in ellipse equation</param>
    /// <param name="bParam"> B parameter in ellipse equation</param>
    /// <param name="numSegments"> number of segments in ellipse</param>
    public Vector3[] GenerateEllipseLinePoints(Vector3[] linePointArray, Vector3 pos, Vector3 normal, float aParam, float bParam, int numSegments)
    {
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
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

![Ellipse Demo 1](https://github.com/markom9822/markom9822.github.io/assets/96113848/26897315-9094-456f-be06-2da2813e48b3)

### Sectors
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

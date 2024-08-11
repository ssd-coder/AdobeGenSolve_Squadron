# Adobe GenSolve - Innovate to Impact

### Installation

1. **Clone the repository:**

   ```bash
   git clone https://github.com/sanketdisale871/AdobeGenSolve.git
   ```

2. # Curvetopia: A Journey into the World of Curves

Welcome to Curvetopia! This project explores the identification, regularization, and beautification of various 2D curves. We focus on developing techniques to handle closed curves, symmetry, and curve completion.

## Objective

Our mission is to create an end-to-end process that takes a line art input in the form of polylines and outputs a set of curves defined as connected sequences of cubic Bézier curves. The primary goals include:

- **Curve Regularization:** Identifying and regularizing basic geometric shapes such as lines, circles, ellipses, rectangles, and polygons.
- **Symmetry Exploration:** Detecting symmetries in closed curves and representing them efficiently.
- **Curve Completion:** Completing incomplete curves that may have gaps due to occlusions or other reasons.

## Problem Description

We simplify the input by using polylines instead of raster images. The input is a finite subset of paths, where each path is a sequence of points. The output is another set of paths with properties of regularization, symmetry, and completeness.

For visualization, the output curve will be in the form of cubic Bézier curves in an SVG format, allowing browser rendering.

## Sections

### 1. Regularize Curves

Identify regular shapes from a set of curves. The task can be broken down into identifying:

- Circles and Ellipses
- Rectangles and Rounded Rectangles
- Regular Polygons
- Star Shapes

### Steps we followed for regularozation

In **Curvetopia**, we use advanced mathematical techniques to regularize (or clean up) curves. Our goal is to take raw data points that form a curve and fit them to more precise and smooth mathematical shapes. Below are the two primary methods we use:

### 1. Line Fitting

To regularize straight lines, we use a technique called **linear regression**. This involves finding the best-fitting straight line through a set of points. The line is represented by the equation:

\[ y = mx + c \]

Where:

- **m** is the slope of the line.
- **c** is the y-intercept, or where the line crosses the y-axis.

Using the given set of points, we calculate the slope (**m**) and the intercept (**c**) using linear algebra. This gives us a smooth line that best represents the original set of points.

### 2. Circle Fitting

For circular shapes, we employ a technique that fits a **circle** to a set of points. This method calculates the center (xc, yc) and the radius (R) of the circle that best fits the points. Here's how it works:

- We first find the average (mean) position of the points, which gives us an initial estimate of the circle's center.
- Then, we adjust this estimate to minimize the distance between the circle and the points.
- Finally, we calculate the radius based on the distance from the center to the points.

This approach ensures that our curve regularization accurately represents the original data while converting it into a perfect circular shape.

### 2. Exploring Symmetry in Curves

In **Curvetopia**, we explore the symmetry in closed curves. Symmetry is an important property in many shapes, and detecting it helps us understand and regularize these shapes more effectively.

### Approach to Detect Symmetry

To detect symmetry in a set of points, we use a straightforward method:

1. **Find the Centroid:**

   - The centroid is the "center of mass" of the points. We calculate the average position of all the points to find this centroid.

2. **Reflect Points Over the Centroid:**

   - We reflect each point across the centroid to see what the shape would look like if it were mirrored. This gives us a new set of "reflected" points.

3. **Compare the Original and Reflected Points:**
   - We then compare the original points to their reflected counterparts. If the points match closely (within a tiny tolerance), we conclude that the shape has symmetry.

## 3. Completing Incomplete Curves

In **Curvetopia**, we handle the challenge of completing incomplete 2D curves. Sometimes, curves have gaps or partial holes due to various reasons, such as occlusions. Our goal is to fill these gaps in a natural and smooth way.

### Approach

We use two main techniques to complete curves depending on the type of occlusion:

1. **Connected Occlusions**
2. **Disconnected Occlusions**

### 1. Connected Occlusions

When the occlusion results in a connected curve, we use interpolation to fill in the gaps. Here’s how it works:

- **Interpolation:** We use a method called **linear interpolation**. This technique estimates the missing parts of the curve by creating a straight line between known points.
- **Steps:**
  - Extract the x and y coordinates from the given points.
  - Use interpolation to estimate the values of y for new x coordinates, filling in the gaps.
  - Create a new set of points that represent the completed curve.

Here’s the Python code snippet for this approach:

```python
from scipy.interpolate import interp1d

def complete_curve(points, occlusion_type='connected'):
    if occlusion_type == 'connected':
        x = points[:, 0]
        y = points[:, 1]
        f = interp1d(x, y, kind='linear')
        new_x = np.linspace(x[0], x[-1], num=500)
        new_y = f(new_x)
        completed_points = np.column_stack((new_x, new_y))
        return completed_points

```

## Handling Disconnected Occlusions in Curves

In **Curvetopia**, we also deal with curves that have disconnected parts. These are cases where the curve is split into separate segments, often due to obstacles or occlusions. Our goal is to reconnect these segments to complete the curve smoothly.

### Approach

When a curve is disconnected, the approach to complete it involves connecting the separate fragments in a natural way. Here’s how we handle this type of occlusion:

1. **Identify the Endpoints:**

   - Determine the starting and ending points of the disconnected segments.

2. **Connect the Endpoints:**
   - Use a linear regression model to create a line that connects these two endpoints.
   - This helps in smoothly bridging the gap between the disconnected segments.

### Steps to Handle Disconnected Occlusions

1. **Find Start and End Points:**

   - Extract the first and last points from the disconnected segments.

2. **Fit a Line Between Endpoints:**

   - Use **Linear Regression** to fit a straight line between these start and end points. This creates a set of points that smoothly connect the segments.

3. **Combine Points:**
   - Combine the original disconnected points with the newly generated points from the linear regression to form a continuous curve.

### Python Code Example

Here’s a Python script that implements the approach for handling disconnected occlusions:

```python
import numpy as np
from sklearn.linear_model import LinearRegression

def handle_disconnected_occlusions(points):
    """
    Handle disconnected occlusions by connecting the endpoints of fragments.
    """
    if len(points) < 2:
        return points

    # Find start and end points
    start_point = points[0]
    end_point = points[-1]

    # Create a line connecting the start and end points
    lin_reg = LinearRegression()
    lin_reg.fit(points[:, 0].reshape(-1, 1), points[:, 1])
    new_x = np.linspace(start_point[0], end_point[0], num=500)
    new_y = lin_reg.predict(new_x.reshape(-1, 1))

    # Concatenate original points with the new points
    completed_points = np.vstack((points, np.column_stack((new_x, new_y))))
    return completed_points

```

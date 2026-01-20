# Graphics Core Package Plan

This document outlines the plan for the `graphics-core` package, providing the foundational graphics object model for ratmath. This package handles abstract drawable primitives, styling, labeling, coordinate systems, and graph generation—independent of any specific rendering target.

## Package Structure

```
packages/graphics-core/
├── src/
│   ├── index.js              # Main exports and registration
│   ├── ratmath-module.js     # Combined VariableManager module
│   ├── primitives.js         # Drawable primitives (path, shape, text)
│   ├── graph.js              # Function graphing and plot generation
│   ├── axes.js               # Axis generation, ticks, gridlines
│   ├── labels.js             # Label positioning and formatting
│   ├── styles.js             # Colors, line styles, fill patterns
│   ├── viewport.js           # Viewport, bounds, coordinate mapping
│   ├── compose.js            # Layer composition, grouping
│   └── transforms.js         # Affine transforms for graphics
├── help/
│   ├── graphics-core.txt     # Main package help
│   ├── graphing.txt          # Function plotting help
│   └── styling.txt           # Style configuration help
├── tests/
│   ├── primitives.test.js
│   ├── graph.test.js
│   ├── axes.test.js
│   └── viewport.test.js
└── package.json
```

---

## Philosophy

Graphics-core is the **abstract representation layer**:
- Produces renderer-agnostic drawable objects
- Handles mathematical coordinate → screen coordinate mapping
- Manages styling, labeling, and composition
- Consumed by `graphics-interactive` (canvas/WebGL) and `graphics-text` (SVG/LaTeX/ASCII)

All coordinates remain as exact rationals until final rendering.

---

## Category 1: Drawable Primitives

### Basic Shapes

| Function | Signature | Description |
|----------|-----------|-------------|
| `GPoint` | `GPoint(x, y, opts?)` | Drawable point marker |
| `GLine` | `GLine(x1, y1, x2, y2, opts?)` | Line segment |
| `GRay` | `GRay(x, y, dx, dy, opts?)` | Ray from point in direction |
| `GInfLine` | `GInfLine(a, b, c, opts?)` | Infinite line ax + by + c = 0 |
| `GCircle` | `GCircle(cx, cy, r, opts?)` | Circle |
| `GArc` | `GArc(cx, cy, r, start, end, opts?)` | Circular arc |
| `GEllipse` | `GEllipse(cx, cy, rx, ry, opts?)` | Ellipse |
| `GRect` | `GRect(x, y, w, h, opts?)` | Rectangle |
| `GPolygon` | `GPolygon(vertices, opts?)` | Polygon from vertex list |
| `GPath` | `GPath(commands, opts?)` | General path (lines, arcs, beziers) |

### Path Commands

```
GPath({
  {"M", 0, 0},          # Move to
  {"L", 1, 1},          # Line to
  {"Q", 2, 0, 3, 1},    # Quadratic bezier
  {"C", 4, 0, 5, 1, 6, 0},  # Cubic bezier
  {"A", 1, 0, 1, 0, 1, 7, 1},  # Arc
  {"Z"}                 # Close path
})
```

### Text & Labels

| Function | Signature | Description |
|----------|-----------|-------------|
| `GText` | `GText(x, y, text, opts?)` | Text at position |
| `GLabel` | `GLabel(obj, text, pos?)` | Label attached to object |
| `GMathText` | `GMathText(x, y, expr, opts?)` | Math expression (LaTeX-style) |
| `GAnnotation` | `GAnnotation(obj, annotation)` | General annotation |

---

## Category 2: Function Graphing

### Basic Plotting

| Function | Signature | Description |
|----------|-----------|-------------|
| `Plot` | `Plot(f, xmin, xmax, opts?)` | Plot function over interval |
| `PlotPolar` | `PlotPolar(r, tmin, tmax, opts?)` | Polar plot r = f(θ) |
| `PlotParam` | `PlotParam(x, y, tmin, tmax, opts?)` | Parametric plot |
| `PlotImplicit` | `PlotImplicit(eq, bounds, opts?)` | Implicit curve F(x,y) = 0 |
| `PlotPoints` | `PlotPoints(data, opts?)` | Scatter plot from data points |
| `PlotDiscrete` | `PlotDiscrete(seq, opts?)` | Plot sequence as discrete points |

### Plot Options

```
Plot(f, -5, 5, {
  samples: 200,           # Number of sample points
  adaptive: 1,            # Adaptive sampling near discontinuities
  discontinuities: {-1, 1},  # Known discontinuity points
  color: "blue",
  lineWidth: 2,
  dashed: 0
})
```

### Multiple Plots

| Function | Signature | Description |
|----------|-----------|-------------|
| `Plots` | `Plots(plot1, plot2, ...)` | Combine multiple plots |
| `PlotFamily` | `PlotFamily(f, param, values, opts?)` | Family of curves |

---

## Category 3: Axes & Grid

### Axis Generation

| Function | Signature | Description |
|----------|-----------|-------------|
| `Axes` | `Axes(opts?)` | Generate x and y axes |
| `XAxis` | `XAxis(opts?)` | Just x-axis |
| `YAxis` | `YAxis(opts?)` | Just y-axis |
| `PolarAxes` | `PolarAxes(opts?)` | Polar coordinate axes |
| `Grid` | `Grid(opts?)` | Coordinate grid |
| `PolarGrid` | `PolarGrid(opts?)` | Polar grid (circles + radial lines) |

### Tick Configuration

```
Axes({
  xmin: -10, xmax: 10,
  ymin: -5, ymax: 5,
  xTick: 1,               # Major tick spacing
  yTick: 1,
  xMinorTicks: 4,         # Minor ticks between majors
  xTickLabels: 1,         # Show tick labels
  xLabel: "x",            # Axis label
  yLabel: "f(x)",
  arrowHeads: 1           # Arrow at axis ends
})
```

### Special Axis Features

| Function | Signature | Description |
|----------|-----------|-------------|
| `CustomTicks` | `CustomTicks(axis, positions, labels?)` | Custom tick positions |
| `LogAxis` | `LogAxis(axis, base?)` | Logarithmic scale |
| `AsymptoteLine` | `AsymptoteLine(x?, y?)` | Dashed asymptote line |

---

## Category 4: Viewport & Coordinate Mapping

### Viewport Management

| Function | Signature | Description |
|----------|-----------|-------------|
| `Viewport` | `Viewport(xmin, xmax, ymin, ymax)` | Define mathematical bounds |
| `ViewportSquare` | `ViewportSquare(center, size)` | Square viewport |
| `ViewportAuto` | `ViewportAuto(objects)` | Auto-fit to objects |
| `ViewportPadding` | `ViewportPadding(vp, padding)` | Add padding |

### Coordinate Transforms

| Function | Signature | Description |
|----------|-----------|-------------|
| `MathToScreen` | `MathToScreen(vp, screen, x, y)` | Math coords → screen coords |
| `ScreenToMath` | `ScreenToMath(vp, screen, sx, sy)` | Screen coords → math coords |
| `AspectRatio` | `AspectRatio(vp, screen)` | Compute aspect ratio |
| `PreserveAspect` | `PreserveAspect(vp, screen)` | Adjust viewport for 1:1 aspect |

---

## Category 5: Styling

### Style Properties

| Property | Values | Description |
|----------|--------|-------------|
| `stroke` | color string | Line/stroke color |
| `fill` | color string | Fill color |
| `strokeWidth` | number | Line width |
| `dashArray` | {on, off, ...} | Dash pattern |
| `opacity` | 0-1 | Transparency |
| `lineCap` | "butt", "round", "square" | Line end style |
| `lineJoin` | "miter", "round", "bevel" | Line join style |
| `markerStart` | marker | Start marker (arrow, etc.) |
| `markerEnd` | marker | End marker |
| `fontSize` | number | Text size |
| `fontFamily` | string | Font family |
| `textAnchor` | "start", "middle", "end" | Text alignment |

### Style Functions

| Function | Signature | Description |
|----------|-----------|-------------|
| `Style` | `Style(opts)` | Create style object |
| `ApplyStyle` | `ApplyStyle(obj, style)` | Apply style to object |
| `MergeStyles` | `MergeStyles(s1, s2)` | Combine styles (s2 overrides) |
| `DefaultStyle` | `DefaultStyle(type)` | Get default for object type |

### Predefined Styles

```
Styles.axis        # Standard axis style
Styles.grid        # Light grid lines
Styles.function    # Default function plot
Styles.point       # Point marker
Styles.label       # Text labels
```

---

## Category 6: Composition & Layers

### Grouping

| Function | Signature | Description |
|----------|-----------|-------------|
| `Group` | `Group(obj1, obj2, ...)` | Group objects together |
| `Layer` | `Layer(name, objects)` | Named layer |
| `Layers` | `Layers(layer1, layer2, ...)` | Stack layers (later = on top) |
| `Figure` | `Figure(viewport, layers)` | Complete figure |

### Clipping & Masking

| Function | Signature | Description |
|----------|-----------|-------------|
| `Clip` | `Clip(objects, region)` | Clip objects to region |
| `ClipToViewport` | `ClipToViewport(objects, vp)` | Clip to viewport bounds |

---

## Category 7: From Geometry

Integration with the geometry package:

| Function | Signature | Description |
|----------|-----------|-------------|
| `ToGraphics` | `ToGraphics(geomObj, opts?)` | Convert geometry object to graphics |
| `DrawPoint` | `DrawPoint(P, opts?)` | Geometry point → GPoint |
| `DrawLine` | `DrawLine(L, opts?)` | Geometry line → GInfLine |
| `DrawCircle` | `DrawCircle(C, opts?)` | Geometry circle → GCircle |
| `DrawPolygon` | `DrawPolygon(poly, opts?)` | Geometry polygon → GPolygon |
| `DrawTriangle` | `DrawTriangle(A, B, C, opts?)` | Triangle with optional centers |

---

## Data Representation

### Graphics Object Structure

```
gobj := GCircle(0, 0, 5)
Set(gobj, "type", "gcircle")
Set(gobj, "cx", 0)
Set(gobj, "cy", 0)
Set(gobj, "r", 5)
Set(gobj, "style", defaultCircleStyle)
Set(gobj, "labels", {})
Set(gobj, "id", uniqueId)
```

### Figure Structure

```
fig := Figure(
  Viewport(-10, 10, -10, 10),
  Layers(
    Layer("grid", Grid()),
    Layer("axes", Axes()),
    Layer("plot", Plot(f, -10, 10)),
    Layer("labels", GLabel(...))
  )
)
```

---

## Output Interface

Graphics-core produces an abstract representation consumed by renderers:

```javascript
// Internal representation (consumed by graphics-text, graphics-interactive)
{
  type: "figure",
  viewport: { xmin, xmax, ymin, ymax },
  layers: [
    {
      name: "grid",
      objects: [{ type: "path", commands: [...], style: {...} }, ...]
    },
    ...
  ]
}
```

---

## Implementation Priority

### Phase 1: Core Primitives
- [ ] GPoint, GLine, GCircle, GRect
- [ ] GPath with basic commands (M, L, Z)
- [ ] GText

### Phase 2: Viewport & Transforms
- [ ] Viewport creation and manipulation
- [ ] Math ↔ screen coordinate mapping
- [ ] Aspect ratio handling

### Phase 3: Function Plotting
- [ ] Basic Plot for single-variable functions
- [ ] Adaptive sampling
- [ ] Discontinuity handling

### Phase 4: Axes & Grid
- [ ] Axes generation with ticks
- [ ] Grid generation
- [ ] Tick label formatting

### Phase 5: Styling
- [ ] Style object model
- [ ] Default styles
- [ ] Style inheritance

### Phase 6: Composition
- [ ] Group, Layer, Figure
- [ ] Clipping

### Phase 7: Geometry Integration
- [ ] ToGraphics for geometry primitives
- [ ] Label positioning helpers

---

## Dependencies

- `@ratmath/core`: Rational arithmetic for coordinates
- `@ratmath/geometry`: Geometric objects (optional, for ToGraphics)
- `@ratmath/stdlib`: Object system (Get, Set)

---

## Open Questions

1. **Coordinate Precision**: When to convert rationals to floats?
   - Proposed: Keep rational until final render, let renderer decide

2. **Adaptive Sampling**: Algorithm for detecting features?
   - Proposed: Sign changes, curvature estimation, user hints

3. **Text Rendering**: How to handle math expressions?
   - Proposed: Pass through to renderer with LaTeX-like format

4. **Animation**: Should graphics-core support animation primitives?
   - Proposed: Not initially; handle in graphics-interactive

5. **3D**: Future 3D support?
   - Proposed: Separate package or major extension later

<!--

author:   riadhBENNESSIB

email:    riadhbennessib@gmail.com

version:  0.0.1

language: fr

narrator: US English Female

comment:  My first liascript in vscode.

script:   https://javascript_resourse_url

script:   https://cdn.jsdelivr.net/gh/liatemplates/pyscript@0.0.4/dist/pyscript.min.js

link:     https://cdn.jsdelivr.net/gh/liatemplates/pyscript@0.0.4/dist/pyscript.css

persistent:  true   


@PyScript.env
<lia-keep>
<py-env>
@0
</py-env>
</lia-keep>
@end

@PyScript.repl: @PyScript.replWith( ,```@0```)

@PyScript.replWith
<lia-keep>
<style>
  .output {
    font-style: inherit;
    font-weight: inherit;
    font-size: inherit;
    line-height: inherit;
    margin-left: 0px;
    float: left;
  }

  .mt-2 {
    margin-left: 0px !important;
    margin-right: 0px !important;
    margin-bottom: 15px !important;
  }

  .editor-box {
    border: 1px solid black;
  }
</style>
<py-repl @0>@1</py-repl>
</lia-keep>
@end
-->

# Hello Liascript 15 juin 2022.

# Liascript on vscode!



# Highlight code
```python
print('Hello World')
```




# pyscript in Liascript!!

``` python @PyScript.replWith(auto-generate="true")
print("Hello pyscript")
```

# pyscript envirennement

``` python @PyScript.env
- matplotlib
- numpy
```

``` python @PyScript.repl
import matplotlib.pyplot as plt
import matplotlib.tri as tri
import numpy as np

# First create the x and y coordinates of the points.
n_angles = 36
n_radii = 8
min_radius = 0.25
radii = np.linspace(min_radius, 0.95, n_radii)

angles = np.linspace(0, 2 * np.pi, n_angles, endpoint=False)
angles = np.repeat(angles[..., np.newaxis], n_radii, axis=1)
angles[:, 1::2] += np.pi / n_angles

x = (radii * np.cos(angles)).flatten()
y = (radii * np.sin(angles)).flatten()
z = (np.cos(radii) * np.cos(3 * angles)).flatten()

# Create the Triangulation; no triangles so Delaunay triangulation created.
triang = tri.Triangulation(x, y)

# Mask off unwanted triangles.
triang.set_mask(np.hypot(x[triang.triangles].mean(axis=1),
                         y[triang.triangles].mean(axis=1))
                < min_radius)

fig1, ax1 = plt.subplots()
ax1.set_aspect('equal')
tpc = ax1.tripcolor(triang, z, shading='flat')
fig1.colorbar(tpc)
ax1.set_title('tripcolor of Delaunay triangulation, flat shading')

fig1
```
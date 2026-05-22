# hello-world

An auto-walking forest scene with scheduled ruby collectibles and gaussian-splat grass under every tree.

Live demo: <http://www.soasme.com/coofygo/scenes/hello-world/>

---

## How it works

On load the camera automatically moves forward through a procedurally-generated forest.  
At the time(s) you specify, glowing ruby gems appear on the left or right side of the path; each one auto-splashes into a particle burst when the camera reaches it.  
Clusters of gaussian-billboard grass splats grow under every tree and wrap seamlessly with the tree pool.

---

## Query parameters

All parameters are optional. Combine them freely: `?level=5-l,10-r&gCount=10&gSize=1.4`

### Ruby schedule

| Param   | Default | Description |
|---------|---------|-------------|
| `level` | _(none)_ | Comma-separated list of `<seconds>-<l\|r>` tokens. Each token spawns a ruby at that elapsed time on the **l**eft or **r**ight path edge. Example: `?level=5-l,10-r,15-l,18-r` |

### Grass splats

| Param      | Default   | Description |
|------------|-----------|-------------|
| `grass`    | `1`       | Set to `0` to disable all grass splats. |
| `gCount`   | `6`       | Number of gaussian splats placed around each tree base. |
| `gSize`    | `1.0`     | World-space diameter of each splat quad (units). |
| `gOpacity` | `0.82`    | Splat opacity, `0` (invisible) → `1` (fully opaque). |
| `gColor`   | `3d8b2f`  | Base grass colour as a **hex string without `#`** (e.g. `5a9e3c`). |
| `gSpread`  | `0.8`     | Scatter radius around the tree base (units). Larger = wider patches. |
| `gDensity` | `8`       | Number of gaussian blobs drawn inside each splat texture. More = thicker/bushier. |

---

## Examples

| Description | Link |
|-------------|------|
| Default forest walk, no rubies | [`/scenes/hello-world/`](http://www.soasme.com/coofygo/scenes/hello-world/) |
| Ruby at 5 s left, 10 s right | [`?level=5-l,10-r`](http://www.soasme.com/coofygo/scenes/hello-world/?level=5-l,10-r) |
| Dense jungle-green grass | [`?gCount=12&gSize=1.3&gDensity=14&gColor=2d7a1f`](http://www.soasme.com/coofygo/scenes/hello-world/?gCount=12&gSize=1.3&gDensity=14&gColor=2d7a1f) |
| Light sparse grass with pale colour | [`?gCount=3&gSize=0.7&gOpacity=0.55&gColor=8fbb60`](http://www.soasme.com/coofygo/scenes/hello-world/?gCount=3&gSize=0.7&gOpacity=0.55&gColor=8fbb60) |
| Disable grass entirely | [`?grass=0`](http://www.soasme.com/coofygo/scenes/hello-world/?grass=0) |
| Full demo: rubies + custom grass | [`?level=5-l,10-r,15-l,20-r&gCount=8&gSize=1.2&gColor=4a9e30`](http://www.soasme.com/coofygo/scenes/hello-world/?level=5-l,10-r,15-l,20-r&gCount=8&gSize=1.2&gColor=4a9e30) |

---

## Technical notes

- **Grass rendering** — single `InstancedBufferGeometry` draw call for all splats across all trees. A custom GLSL billboard vertex shader keeps quads camera-facing in view space. Fragment shader applies matching `FogExp2` (density `0.035`) so splats fade with the forest.
- **Splat texture** — procedurally generated on a `128×128` canvas: `gDensity` overlapping radial-gradient ellipses with bright centres and transparent edges, mimicking a real 2D Gaussian.  
- **Tree pool wrapping** — grass world positions are recomputed every frame after trees teleport, keeping splats glued to their tree at zero extra draw calls.
- **GPU preload** — all textures and shaders (including the grass `ShaderMaterial`) are compiled with `renderer.compile()` behind the loading screen, so there is no first-frame stutter.

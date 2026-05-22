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

### Grass blades

| Param        | Default   | Description |
|--------------|-----------|-------------|
| `grass`      | `1`       | Set to `0` to disable all grass. |
| `gCount`     | `80`      | Grass blades per tree. |
| `gHeight`    | `0.55`    | Blade height in world units. |
| `gWidth`     | `0.12`    | Blade base width in world units. |
| `gSpread`    | `1.0`     | Scatter radius around the tree base (units). |
| `gBase`      | `1a3d0a`  | Root/base colour, hex without `#`. |
| `gTip`       | `6fc93a`  | Tip colour, hex without `#`. |
| `gWind`      | `0.35`    | Wind bend strength. |
| `gWindSpeed` | `0.8`     | Wind animation speed. |

---

## Examples

| Description | Link |
|-------------|------|
| Default forest walk, no rubies | [`/scenes/hello-world/`](http://www.soasme.com/coofygo/scenes/hello-world/) |
| Ruby at 5 s left, 10 s right | [`?level=5-l,10-r`](http://www.soasme.com/coofygo/scenes/hello-world/?level=5-l,10-r) |
| Dense tall grass | [`?gCount=150&gHeight=0.8&gWidth=0.10`](http://www.soasme.com/coofygo/scenes/hello-world/?gCount=150&gHeight=0.8&gWidth=0.10) |
| Light sparse grass | [`?gCount=30&gHeight=0.3&gSpread=0.6`](http://www.soasme.com/coofygo/scenes/hello-world/?gCount=30&gHeight=0.3&gSpread=0.6) |
| Autumn yellow-brown grass | [`?gBase=5c3d00&gTip=c8a020`](http://www.soasme.com/coofygo/scenes/hello-world/?gBase=5c3d00&gTip=c8a020) |
| Strong swaying wind | [`?gWind=0.9&gWindSpeed=1.8`](http://www.soasme.com/coofygo/scenes/hello-world/?gWind=0.9&gWindSpeed=1.8) |
| Disable grass entirely | [`?grass=0`](http://www.soasme.com/coofygo/scenes/hello-world/?grass=0) |
| Full demo: rubies + custom grass | [`?level=5-l,10-r,15-l,20-r&gCount=100&gHeight=0.65&gBase=1a3d0a&gTip=80e830`](http://www.soasme.com/coofygo/scenes/hello-world/?level=5-l,10-r,15-l,20-r&gCount=100&gHeight=0.65&gBase=1a3d0a&gTip=80e830) |

---

## Technical notes

- **Grass rendering** — real 3-column tapered blade geometry (5 segments, curved profile) instanced across all tree bases in a single draw call. Technique adapted from [green-grass-v3.vercel.app](https://green-grass-v3.vercel.app/) (MIT). Each blade has per-instance scale, rotation, and seed attributes.
- **Wind animation** — simplex noise (2D) samples the world XZ position + time to create organic non-uniform swaying. Bend is zero at the root and quadratic toward the tip, so roots stay pinned.
- **Blade texture** — procedurally generated bezier leaf silhouette on a `128×128` canvas (white on black), used as an alpha mask so each blade has a natural tapered shape.
- **Colour gradient** — GLSL mixes a dark root colour → `gBase` → `gTip` along `uv.y` with fake root AO (deep roots stay dark).
- **Tree-pool wrapping** — grass world positions are recomputed every frame after trees teleport, keeping blades glued to their parent tree.

# 2D Unity Development

Unity's 2D stack is specialized rather than being “3D with one axis ignored”.

## Typical 2D object

~~~text
Player
├── Transform
├── SpriteRenderer
├── Rigidbody2D
├── CapsuleCollider2D
└── PlayerController
~~~

## 2D vs 3D

| Concept | 2D | 3D |
|---|---|---|
| Physics body | Rigidbody2D | Rigidbody |
| Collider | Collider2D | Collider |
| Raycast result | RaycastHit2D | RaycastHit |
| Vector position | Vector2 | Vector3 |
| Rotation | Usually Z angle | Full 3-axis rotation |
| Renderer | SpriteRenderer | MeshRenderer / SkinnedMeshRenderer |
| Joints | Joint2D family | Joint family |
| Physics world | Physics2D | Physics |

Do not mix 2D and 3D physics components expecting them to interact.

## 2D project choices

Choose 2D when the game primarily operates in an XY plane and uses sprites or Tilemaps.

Common genres:

- platformers
- top-down games
- roguelikes
- puzzle games
- 2D fighters
- physics games

## SpriteRenderer

Important properties:

| Property | Meaning |
|---|---|
| sprite | Sprite asset to display |
| color | Tint / alpha |
| flipX / flipY | Visual mirroring |
| sortingLayerName | Draw order group |
| sortingOrder | Order inside sorting layer |
| drawMode | Simple / sliced / tiled style |
| maskInteraction | Interaction with SpriteMask |

~~~csharp
var renderer = GetComponent<SpriteRenderer>();
renderer.flipX = moveInput.x < 0f;
~~~

## Sorting

For 2D, visual order is often more important than Z position alone.

Use:

- Sorting Layers for broad groups.
- Sorting Order for local stacking.
- Z only when it fits your camera/rendering setup.

A practical scheme:

| Sorting Layer | Example |
|---|---|
| Background | Sky, distant art |
| World | Tiles, level |
| Characters | Player, enemies |
| Effects | Explosions, particles |
| Foreground | Overlays |
| UI | Screen-space UI |

## Pixel art

Typical considerations:

- consistent Pixels Per Unit
- correct filter mode
- avoid unnecessary texture resampling
- use appropriate camera settings
- be deliberate about sprite pivots

## Orthographic camera

2D games commonly use an orthographic Camera.

~~~csharp
Camera cam = Camera.main;
cam.orthographic = true;
~~~

The orthographic size determines the visible vertical half-size of the camera view.

## 2D gameplay architecture

A clean player can be split into:

~~~text
Player
├── Visuals
├── Physics
├── Input
├── Motor
├── Combat
└── Health
~~~

Keep visual presentation separate from physics when possible. This makes flipping, animation and effects easier to modify without changing collision behavior.

## Physics movement

Use Rigidbody2D movement in FixedUpdate or through physics-friendly APIs.

For direct velocity control:

~~~csharp
private void FixedUpdate()
{
    Vector2 velocity = body.linearVelocity;
    velocity.x = moveInput.x * speed;
    body.linearVelocity = velocity;
}
~~~

For force-driven movement:

~~~csharp
body.AddForce(Vector2.right * moveInput.x * acceleration);
~~~

## Ground detection

A small downward query is often more stable than assuming any collision means “grounded”.

~~~csharp
bool grounded = Physics2D.Raycast(
    transform.position,
    Vector2.down,
    groundCheckDistance,
    groundMask
);
~~~

For wider feet, use CircleCast or overlap tests.

## 2D camera follow

Use LateUpdate so the camera reacts after gameplay movement.

~~~csharp
private void LateUpdate()
{
    Vector3 p = target.position;
    p.z = transform.position.z;
    transform.position = p;
}
~~~

For damping, use SmoothDamp rather than instantly snapping.

## Tilemaps

Tilemaps are designed for grid-based 2D level construction. They can represent:

- ground
- walls
- decorations
- hazards
- collision
- gameplay markers

A tilemap with a TilemapCollider2D can be combined with CompositeCollider2D for fewer effective collision shapes.

## Common mistakes

| Symptom | Likely cause |
|---|---|
| Rigidbody2D does not collide with Rigidbody | 2D/3D component mismatch |
| Player jitters while moving | Transform manipulation of a physics body |
| Player falls through floor | Missing/incorrect collider or layer filtering |
| Sprite draws behind floor | Sorting layer/order |
| Camera feels jittery | Follow done in Update instead of LateUpdate, or inconsistent interpolation |
| Raycast misses | Wrong layer mask / origin / direction / distance |

## Related

[2d-physics.md](2d-physics.md) · [2d-graphics.md](2d-graphics.md) · [input.md](input.md) · [math.md](math.md)

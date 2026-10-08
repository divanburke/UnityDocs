# 2D Graphics, Tilemaps and Animation

## Sprite import pipeline

A 2D image normally follows:

~~~text
Texture Asset
   ↓
Texture Import Settings
   ↓
Sprite / Sprite Sheet
   ↓
SpriteRenderer
   ↓
Camera
~~~

## Sprite import decisions

| Setting | Why it matters |
|---|---|
| Texture Type = Sprite (2D and UI) | Enables sprite workflow |
| Sprite Mode | Single vs Multiple |
| Pixels Per Unit | World size |
| Filter Mode | Crisp vs smooth sampling |
| Compression | Size vs quality |
| Mip Maps | Often unnecessary for flat pixel sprites |
| Pivot | Rotation / placement anchor |

## SpriteRenderer

Useful runtime members:

~~~csharp
SpriteRenderer spriteRenderer;

private void Awake()
{
    spriteRenderer = GetComponent<SpriteRenderer>();
}

private void Face(float x)
{
    if (Mathf.Abs(x) > 0.01f)
    {
        spriteRenderer.flipX = x < 0f;
    }
}
~~~

## Sorting

Use sorting layers for broad visual groups and sorting order for local ordering.

A common player setup:

~~~text
Background  0
World       0
Characters  0..100
Effects     0..1000
Foreground  0..100
UI          Canvas system
~~~

## Tilemap architecture

A practical tilemap set can be:

~~~text
Grid
├── Ground
├── GroundDecor
├── Foreground
└── Collision
~~~

TilemapCollider2D generates collision shapes from tiles. CompositeCollider2D can combine adjacent shapes.

## Tilemap scripting concepts

Depending on package/version, runtime operations commonly include reading and writing cells, converting between cell and world coordinates, and refreshing tile visuals.

Conceptual pattern:

~~~csharp
Vector3Int cell = tilemap.WorldToCell(worldPosition);
tilemap.SetTile(cell, tile);
~~~

Use Tilemap.CellToWorld and WorldToCell deliberately: cell coordinates and world coordinates are different spaces.

## Rule Tiles and procedural levels

For a large project, separate:

- level data
- tile appearance
- tile collision
- procedural generation rules

Do not put game logic directly into visual tile assets when a data-driven system would be easier to maintain.

## 2D lights

Unity's 2D renderer supports 2D lighting workflows in appropriate render pipeline/package configurations.

Common concepts:

- Light2D
- normal maps
- shadow-casting sprites
- sorting layers

Use the pipeline and package version configured by the project rather than assuming every Light2D feature exists in every renderer.

## Animation options

| Method | Best for |
|---|---|
| Animator Controller | Character state machines |
| Sprite swapping | Small/simple frame animations |
| AnimationClip | Timed property animation |
| Timeline | Sequencing |
| Script-driven | Highly procedural motion |

## Animator sprite workflow

A common setup:

~~~text
Player
├── Rigidbody2D
├── Collider2D
└── Visual
    ├── SpriteRenderer
    └── Animator
~~~

This keeps physics independent from visual animation.

## Pixel-perfect style

For pixel art, consistency matters more than a single checkbox.

Keep consistent:

- PPU
- sprite dimensions
- camera behavior
- asset filtering
- transform scaling

Avoid repeatedly scaling pixel sprites to arbitrary fractional values unless that is intentional.

## 2D shadows and depth

A visually front object does not have to be physically “in front”. Keep rendering order and gameplay collision logic separate.

## Common bugs

| Bug | Fix |
|---|---|
| Sprite looks blurry | Check Filter Mode and scaling |
| Sprite appears behind another | Sorting Layer / Order |
| Tile collision has gaps | Inspect tile collider and composite settings |
| Sprite flips around wrong point | Correct pivot |
| Character shadow follows oddly | Separate visual/shadow hierarchy |
| Animation moves collider unexpectedly | Separate visual from physics or review root motion setup |

## Related

[2d-overview.md](2d-overview.md) · [2d-physics.md](2d-physics.md) · [animation.md](animation.md) · [3d-rendering.md](3d-rendering.md)

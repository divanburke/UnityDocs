# Unity Component Catalog

Use this as an Inspector-oriented lookup: find the component, then jump to its dedicated topic.

## Core components

| Component | Main job | 2D | 3D |
|---|---|:---:|:---:|
| Transform | Position, rotation, scale, hierarchy | Yes | Yes |
| Rigidbody2D | 2D physics body | Yes | No |
| Rigidbody | 3D physics body | No | Yes |
| Collider2D | 2D collision shape | Yes | No |
| Collider | 3D collision shape | No | Yes |
| Animator | Animation state machine | Yes | Yes |
| ParticleSystem | Particle effects | Yes | Yes |
| AudioSource | Plays audio | Yes | Yes |
| Camera | Renders a view | Yes | Yes |
| Light | Lighting | 2D lights vary by renderer | Yes |
| Canvas | Classic UI root | Yes | Yes |
| EventSystem | UI input dispatch | Yes | Yes |

## Transform

Every GameObject has a Transform.

Key members:

| Member | Meaning |
|---|---|
| position | World position |
| localPosition | Parent-relative position |
| rotation | World Quaternion |
| localRotation | Parent-relative Quaternion |
| localScale | Parent-relative scale |
| parent | Parent Transform |
| childCount | Direct child count |
| forward/right/up | Local axes in world space |

Use Transform for non-physics objects or as the spatial representation of systems that own movement elsewhere.

## Rigidbody2D

Use when an object should participate in the 2D physics simulation.

Good for:

- players
- crates
- balls
- moving platforms
- ragdoll parts

Bad fit:

- purely visual objects
- UI
- static background graphics

Important Unity 6 property:

~~~csharp
body.linearVelocity
~~~

## Rigidbody

3D physics body.

~~~csharp
using UnityEngine;

[RequireComponent(typeof(Rigidbody))]
public class Example : MonoBehaviour
{
    [SerializeField] private float push = 5f;

    private Rigidbody body;

    private void Awake()
    {
        body = GetComponent<Rigidbody>();
    }

    private void FixedUpdate()
    {
        body.AddForce(transform.forward * push);
    }
}
~~~

## Collider2D family

| Component | Typical use |
|---|---|
| BoxCollider2D | Platforms, boxes |
| CircleCollider2D | Balls, radial objects |
| CapsuleCollider2D | Characters |
| PolygonCollider2D | Irregular outline |
| EdgeCollider2D | Lines/platform edges |
| CompositeCollider2D | Combine nearby shapes |
| TilemapCollider2D | Tile-based worlds |

## 3D Collider family

| Component | Typical use |
|---|---|
| BoxCollider | Props, walls |
| SphereCollider | Balls, triggers |
| CapsuleCollider | Characters |
| MeshCollider | Detailed static geometry |
| WheelCollider | Vehicle wheels |
| TerrainCollider | Terrain |

## MeshFilter

Stores a reference to a Mesh for a MeshRenderer.

~~~csharp
MeshFilter filter = GetComponent<MeshFilter>();
Mesh mesh = filter.sharedMesh;
~~~

## MeshRenderer

Draws a mesh using materials.

Key properties include:

- enabled
- materials
- shadow casting mode
- receive shadows
- light probes
- reflection probes where supported

## SkinnedMeshRenderer

Used for animated/deformed meshes.

Key concepts:

- shared mesh
- bones
- root bone
- bind poses
- materials
- bounds

If a character vanishes while animated, inspect the renderer bounds.

## CharacterController

A capsule-like kinematic character solution.

~~~csharp
Vector3 motion = new Vector3(
    input.x,
    verticalVelocity,
    input.y
);

controller.Move(motion * Time.deltaTime);
~~~

CharacterController does not behave like a dynamic Rigidbody. Gravity and movement are normally handled by your controller logic.

## Animator

Attach to the visual hierarchy that owns the animation.

Common members:

| Member | Use |
|---|---|
| SetFloat | Continuous numeric parameter |
| SetBool | Persistent state |
| SetInteger | Multi-state integer |
| SetTrigger | One-shot transition |
| ResetTrigger | Cancel trigger |
| Play | Direct state playback |
| CrossFade | Smooth state transition |
| Update | Manually evaluate animator |

## SpriteRenderer

The main component for displaying a 2D sprite.

Useful properties:

- sprite
- color
- flipX / flipY
- sortingLayerName
- sortingOrder
- drawMode
- size

## ParticleSystem

Useful for short-lived effects:

- smoke
- sparks
- dust
- magic-like effects
- hit flashes

A particle system has modules for:

- Main
- Emission
- Shape
- Velocity over Lifetime
- Color over Lifetime
- Size over Lifetime
- Renderer

Do not enable every module by default. Feature complexity has a cost.

## Camera

Cameras define what reaches the screen.

Important properties:

| Property | Use |
|---|---|
| projection | Perspective / Orthographic |
| fieldOfView | Perspective view angle |
| orthographicSize | Orthographic view size |
| nearClipPlane | Near visibility |
| farClipPlane | Far visibility |
| cullingMask | Layers rendered |
| depth | Ordering in compatible workflows |

## AudioSource

Key runtime methods:

~~~csharp
source.Play();
source.Stop();
source.Pause();
source.UnPause();
source.PlayOneShot(clip);
~~~

## Canvas

Choose render mode based on UI purpose.

| Mode | Typical use |
|---|---|
| Overlay | HUD/menu |
| Camera | Camera-relative UI |
| World Space | UI in the world |

## EventSystem

Routes pointer, navigation and submit/cancel events to UI systems. A missing or incompatible EventSystem/input module is a common reason for non-interactive UI.

## Light

Use lights according to the render pipeline.

For a 3D scene, start simple:

~~~text
Directional Light
+ Ambient/environment contribution
+ Small number of local lights
~~~

Then add complexity based on the look and measured performance.

## LayerMask-friendly components

For physics and camera behavior, layers are a major control surface.

Example:

~~~csharp
[SerializeField] private LayerMask enemyMask;
~~~

Then:

~~~csharp
Collider[] hits = Physics.OverlapSphere(
    transform.position,
    2f,
    enemyMask
);
~~~

## Required-component pattern

~~~csharp
[RequireComponent(typeof(Collider2D))]
[RequireComponent(typeof(Rigidbody2D))]
public class PhysicsActor2D : MonoBehaviour
{
}
~~~

This documents and enforces dependencies.

## Component design rule

A component should answer one main question.

Good:

~~~text
PlayerInput
PlayerMotor
PlayerCombat
PlayerHealth
PlayerAnimation
~~~

Less maintainable:

~~~text
PlayerEverythingController
~~~

A larger controller can still coordinate systems, but keep reusable responsibilities separated.

## Related

[fundamentals.md](fundamentals.md) · [api-core.md](api-core.md) · [2d-physics.md](2d-physics.md) · [3d-physics.md](3d-physics.md)

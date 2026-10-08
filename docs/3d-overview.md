# 3D Unity Development

## Typical 3D hierarchy

~~~text
Player
├── Transform
├── Character controller / Rigidbody
├── Collider
├── Visual
│   └── MeshRenderer / SkinnedMeshRenderer
└── PlayerController
~~~

## Core 3D concepts

Unity's 3D world is built around:

- Transform hierarchy
- meshes
- materials
- cameras
- lights
- colliders
- Rigidbody physics
- animation
- navigation

## Meshes

A Mesh describes geometry:

- vertices
- triangles
- normals
- UVs
- optional colors/tangents

MeshFilter supplies geometry to a renderer. MeshRenderer draws it.

SkinnedMeshRenderer deforms a mesh using bones for character animation.

## Materials

A material references a shader and supplies parameters such as:

- base color
- textures
- metallic
- smoothness
- emission
- normal information

Exact available parameters depend on the active render pipeline and shader.

## 3D Transform

~~~csharp
transform.position += transform.forward * speed * Time.deltaTime;
~~~

Remember: <code>forward</code>, <code>right</code>, and <code>up</code> are local directions transformed into world space.

## Movement styles

| Style | Typical implementation |
|---|---|
| Simple physics | Rigidbody + forces |
| Precise physics | Rigidbody velocity / MovePosition |
| Character controller | CharacterController.Move |
| Nav agent | NavMeshAgent |
| Root-motion character | Animator-driven movement |
| Flying arcade | Transform or custom kinematic system |

Choose one owner for movement. Multiple systems fighting over Transform.position are a common source of jitter.

## CharacterController

CharacterController is a collision-aware kinematic controller rather than a normal dynamic Rigidbody.

~~~csharp
controller.Move(motion * Time.deltaTime);
~~~

Gravity must normally be handled by your controller code.

## Rigidbody

Unity 6 uses <code>linearVelocity</code> on Rigidbody APIs.

~~~csharp
Vector3 v = body.linearVelocity;
v.x = move.x * speed;
body.linearVelocity = v;
~~~

Physics-force movement belongs in FixedUpdate.

## Cameras

A perspective camera projects depth. An orthographic camera keeps objects the same apparent size regardless of distance.

Perspective controls:

- field of view
- near/far clipping
- aspect

Orthographic controls:

- orthographic size

## Lighting

Main light types:

| Light | Use |
|---|---|
| Directional | Sun / global direction |
| Point | Local spherical light |
| Spot | Cone of light |
| Area | Large soft-looking sources, pipeline dependent |

Realistic lighting is a balance between light count, shadows, material response and render pipeline cost.

## Layers

Layers are heavily used in 3D:

- physics collision filtering
- camera culling
- raycast filtering
- editor organization

## Raycasting

Common pattern:

~~~csharp
Ray ray = Camera.main.ScreenPointToRay(Input.mousePosition);

if (Physics.Raycast(ray, out RaycastHit hit, 100f))
{
    Debug.Log(hit.collider.name);
}
~~~

Use a layer mask to avoid hitting irrelevant objects.

## Ground detection

For precise character grounding, prefer an intentional ground query rather than assuming any contact is a floor.

Possibilities:

- Raycast
- SphereCast
- CapsuleCast
- OverlapSphere

## 3D project structure

For larger projects:

~~~text
Player
├── Root
├── Physics
├── Visual
├── CameraTarget
├── Interaction
└── Systems
~~~

Keep data and systems modular. A player should not become a 2,000-line controller just because the project grew.

## Common mistakes

| Problem | Likely cause |
|---|---|
| Character rotates unexpectedly | Rigidbody torque / free rotation |
| Camera clips through geometry | Near clip plane / collision strategy |
| Raycast hits player | No layer mask |
| Physics jitter | Transform changes mixed with Rigidbody |
| Model black or pink | Material/shader/render-pipeline mismatch |
| Character slides forever | Friction/movement design |

## Related

[3d-physics.md](3d-physics.md) · [3d-rendering.md](3d-rendering.md) · [animation.md](animation.md) · [ai-navigation.md](ai-navigation.md)

# 3D Physics Reference

## Rigidbody

Rigidbody makes a GameObject participate in 3D physics.

Important properties:

| Property | Purpose |
|---|---|
| mass | Inertial mass |
| linearVelocity | Current velocity |
| angularVelocity | Angular velocity |
| linearDamping | Slows linear motion |
| angularDamping | Slows rotation |
| useGravity | Gravity participation |
| isKinematic | Disables normal dynamic response |
| interpolation | Smoother visual motion |
| collisionDetectionMode | Collision detection strategy |
| constraints | Freeze axes |

Unity 6 note: current API uses <code>linearVelocity</code>; older tutorials may show <code>velocity</code>.

## AddForce

~~~csharp
body.AddForce(Vector3.forward * thrust, ForceMode.Force);
~~~

Force modes:

| Mode | Behavior |
|---|---|
| Force | Continuous mass-aware force |
| Acceleration | Continuous force ignoring mass |
| Impulse | Instant mass-aware velocity change |
| VelocityChange | Instant velocity change ignoring mass |

Use FixedUpdate for physics-driven force application.

## Rigidbody methods

| Method | Purpose |
|---|---|
| AddForce | Add force |
| AddForceAtPosition | Add force + torque effect |
| AddTorque | Add rotational force |
| MovePosition | Move physics body |
| MoveRotation | Move physics rotation |
| Sleep / WakeUp | Control sleeping |
| GetPointVelocity | Velocity at world point |
| ClosestPointOnBounds | Approximate closest bounds point in older APIs; prefer current collider methods for exact queries |

## 3D colliders

| Collider | Shape |
|---|---|
| BoxCollider | Box |
| SphereCollider | Sphere |
| CapsuleCollider | Capsule |
| MeshCollider | Mesh surface |
| WheelCollider | Vehicle wheel simulation |
| TerrainCollider | Terrain |

For dynamic Rigidbody objects, primitive colliders are generally simpler and cheaper than arbitrary MeshColliders.

## Convex MeshCollider

Dynamic MeshCollider use has restrictions. For moving physics bodies, a convex mesh is the usual compatible approach where a primitive cannot approximate the shape well.

Do not use a complex non-convex MeshCollider as a general-purpose dynamic character collider.

## Collision callbacks

~~~csharp
private void OnCollisionEnter(Collision collision)
{
    ContactPoint contact = collision.GetContact(0);

    Debug.Log(
        $"Hit {collision.collider.name} with normal {contact.normal}"
    );
}

private void OnCollisionExit(Collision collision)
{
}
~~~

## Trigger callbacks

~~~csharp
private void OnTriggerEnter(Collider other)
{
    if (other.CompareTag("Player"))
    {
        Debug.Log("Player entered");
    }
}
~~~

## Physics queries

| API | Use |
|---|---|
| Physics.Raycast | Thin line query |
| Physics.RaycastAll | All line hits |
| Physics.SphereCast | Swept sphere |
| Physics.BoxCast | Swept box |
| Physics.CapsuleCast | Swept capsule |
| Physics.OverlapSphere | Area test |
| Physics.OverlapBox | Box volume test |
| Physics.CheckSphere | Boolean sphere test |
| Physics.CheckCapsule | Boolean capsule test |

## Raycast pattern

~~~csharp
if (Physics.Raycast(
    origin,
    direction,
    out RaycastHit hit,
    maxDistance,
    layerMask,
    QueryTriggerInteraction.Ignore))
{
    // Use hit.collider, hit.point, hit.normal, hit.distance.
}
~~~

## RaycastHit essentials

| Member | Meaning |
|---|---|
| collider | Collider that was hit |
| rigidbody | Rigidbody, if present |
| point | Hit point in world space |
| normal | Surface normal |
| distance | Distance from query origin |
| transform | Hit collider's Transform |

## Collision filtering

Three major filters often work together:

1. Layer Collision Matrix
2. Query layer mask
3. QueryTriggerInteraction

Make collision rules explicit.

## Joints

Common joints:

| Joint | Use |
|---|---|
| FixedJoint | Rigid connection |
| HingeJoint | Rotational connection |
| SpringJoint | Spring-like behavior |
| ConfigurableJoint | Highly configurable constraint |
| CharacterJoint | Ragdoll-style body connection |
| Fixed / configurable constraints | Mechanical structures |

For ragdolls, joint limits and drive strength are crucial to stability.

## Physics materials

PhysicMaterial can control friction and bounciness.

Use realistic values where possible. Extremely high friction or bounce often creates unstable behavior rather than solving a controller problem.

## Continuous collision detection

For fast-moving objects, discrete detection can miss contacts.

Use continuous modes selectively for:

- projectiles
- small fast objects
- important high-speed collisions

Do not put every Rigidbody into the most expensive mode.

## Sleep

Unity can let bodies sleep when sufficiently still. This saves work.

Unexpected “frozen” behavior can come from:

- sleeping
- constraints
- kinematic state
- script not updating velocity
- collision filters

## Physics timestep

Physics updates are simulation steps, not render frames. Apply force and read/update physics state with the correct timing.

## Physics debugging checklist

~~~text
1. Is there the correct 2D/3D Rigidbody?
2. Is there a compatible Collider?
3. Is the Collider enabled?
4. Is the Rigidbody dynamic/kinematic as intended?
5. Are layers allowed to collide?
6. Is the object asleep?
7. Is another script overwriting movement?
8. Is collision detection appropriate for the speed?
9. Is a trigger being mistaken for a solid collider?
10. Is the physics update happening in FixedUpdate?
~~~

## Related

[3d-overview.md](3d-overview.md) · [math.md](math.md) · [debugging.md](debugging.md) · [performance.md](performance.md)

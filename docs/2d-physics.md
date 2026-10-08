# 2D Physics Reference

Unity 2D physics uses its own component family and physics world.

## Rigidbody2D

Core properties:

| Property | Meaning |
|---|---|
| bodyType | Dynamic, Kinematic, Static |
| position | Physics position |
| rotation | Physics rotation |
| linearVelocity | Linear velocity |
| angularVelocity | Angular velocity |
| mass | Mass |
| linearDamping | Linear drag/damping |
| angularDamping | Angular damping |
| gravityScale | Gravity multiplier |
| collisionDetectionMode | Continuous/discrete-style detection |
| interpolation | Visual smoothing between physics steps |
| constraints | Freeze selected axes |
| sleepingMode | Sleep behavior |

Important Unity 6 note: use <code>linearVelocity</code> for current Rigidbody2D velocity APIs. Older tutorials often show <code>velocity</code>.

## Moving a Rigidbody2D

### Direct velocity

Good for arcade controllers.

~~~csharp
private void FixedUpdate()
{
    body.linearVelocity = new Vector2(
        moveInput.x * speed,
        body.linearVelocity.y
    );
}
~~~

### AddForce

Good when acceleration and physical response matter.

~~~csharp
body.AddForce(Vector2.right * acceleration);
~~~

### MovePosition

Useful for controlled kinematic-style movement.

~~~csharp
body.MovePosition(body.position + delta);
~~~

Do not mix multiple movement strategies casually. For example, setting position, velocity, and adding force in the same step can fight itself.

## Rigidbody2D methods

| Method | Purpose |
|---|---|
| AddForce | Apply force |
| AddForceAtPosition | Force + point, creating torque |
| AddTorque | Apply rotational force |
| MovePosition | Move toward a physics position |
| MoveRotation | Move toward a rotation |
| Sleep | Manually sleep |
| WakeUp | Wake body |
| Cast | Cast attached colliders |
| IsTouching | Check touching state |

## Colliders

| Component | Shape / use |
|---|---|
| BoxCollider2D | Rectangle |
| CircleCollider2D | Circle |
| CapsuleCollider2D | Capsule |
| PolygonCollider2D | Arbitrary polygon |
| EdgeCollider2D | Open line/edge |
| CompositeCollider2D | Combines child collider geometry |
| TilemapCollider2D | Tilemap collision |

## Collider2D settings

| Property | Meaning |
|---|---|
| Is Trigger | Detect overlaps without normal collision response |
| Used by Effector | Allows 2D effectors |
| Offset | Shape offset from Transform |
| Material | Friction/bounce behavior |
| Layer | Which collision category it belongs to |

## Collision callbacks

Use when physical contact matters.

~~~csharp
private void OnCollisionEnter2D(Collision2D collision)
{
    if (collision.collider.CompareTag("Ground"))
    {
        // Contact just started.
    }
}

private void OnCollisionStay2D(Collision2D collision)
{
}

private void OnCollisionExit2D(Collision2D collision)
{
}
~~~

Useful data:

~~~csharp
ContactPoint2D contact = collision.GetContact(0);
Vector2 normal = contact.normal;
Vector2 point = contact.point;
float impulse = contact.normalImpulse;
~~~

## Trigger callbacks

~~~csharp
private void OnTriggerEnter2D(Collider2D other)
{
    if (other.CompareTag("Pickup"))
    {
        Destroy(other.gameObject);
    }
}
~~~

A trigger is a sensor volume.

## Physics2D query table

| API | Returns | Best for |
|---|---|---|
| Physics2D.Raycast | One RaycastHit2D | Ground checks, line checks |
| Physics2D.RaycastAll | All hits | Multiple surfaces / enemies |
| Physics2D.CircleCast | One hit | Thick sweep |
| Physics2D.CircleCastAll | All hits | Thick multi-hit sweep |
| Physics2D.OverlapCircle | One Collider2D | Instant area test |
| Physics2D.OverlapCircleAll | All colliders | Area attacks, detection |
| Physics2D.OverlapCapsule | One collider | Character-shaped test |
| Physics2D.OverlapBox | One collider | Box sensor |
| Physics2D.OverlapArea | One collider | Rectangular region |

### Raycast

~~~csharp
if (Physics2D.Raycast(
    origin,
    Vector2.down,
    distance,
    groundMask
))
{
    // Ground detected.
}
~~~

### RaycastAll

~~~csharp
RaycastHit2D[] hits = Physics2D.RaycastAll(
    origin,
    direction,
    distance,
    layerMask
);

foreach (RaycastHit2D hit in hits)
{
    Debug.Log(hit.collider.name);
}
~~~

Array-returning “All” methods allocate result arrays. For high-frequency queries, use lower-allocation patterns such as NonAlloc/query overloads where available.

## Layer masks

~~~csharp
[SerializeField] private LayerMask groundMask;
~~~

This is usually cleaner than hard-coded layer integers.

## Physics materials

A PhysicsMaterial2D can control surface interaction such as friction and bounciness.

Use it for:

- ice
- rubber
- sticky surfaces
- pinball-like surfaces

Avoid solving every movement problem with extreme friction values.

## Joints

Common 2D joints:

| Joint | Typical use |
|---|---|
| FixedJoint2D | Lock bodies together |
| HingeJoint2D | Rotate around an anchor |
| DistanceJoint2D | Maintain distance |
| SpringJoint2D | Spring-like connection |
| RelativeJoint2D | Maintain relative motion |
| SliderJoint2D | Constrain along an axis |
| WheelJoint2D | Vehicle suspension |
| TargetJoint2D | Pull body toward point |

For ragdolls, HingeJoint2D and configurable limits are especially useful.

## CompositeCollider2D

If many child colliders form one surface, CompositeCollider2D can combine shapes.

Typical tilemap setup:

~~~text
Tilemap
├── TilemapCollider2D
└── CompositeCollider2D
~~~

This can reduce many small adjacent collision shapes into a cleaner composite representation.

## Fixed timestep

Physics runs on a fixed simulation interval. Do not assume one rendered frame equals one physics step.

Use:

~~~csharp
private void FixedUpdate()
{
    // Physics work.
}
~~~

Use interpolation when visual smoothness is needed for physics-driven bodies.

## Debugging 2D physics

Draw your query.

~~~csharp
Debug.DrawRay(origin, direction * distance, Color.green);
~~~

Also inspect:

- Project Physics 2D settings
- Layer Collision Matrix
- Rigidbody2D body type
- Collider enabled state
- Trigger state
- object scale
- sleeping / interpolation settings

## Practical controller pattern

~~~csharp
using UnityEngine;

[RequireComponent(typeof(Rigidbody2D))]
public class SimplePlatformerMotor : MonoBehaviour
{
    [SerializeField] private float speed = 7f;
    [SerializeField] private float jumpSpeed = 12f;
    [SerializeField] private LayerMask groundMask;
    [SerializeField] private Transform groundPoint;
    [SerializeField] private float groundRadius = 0.12f;

    private Rigidbody2D body;
    private float moveInput;

    private void Awake()
    {
        body = GetComponent<Rigidbody2D>();
    }

    private void Update()
    {
        moveInput = Input.GetAxisRaw("Horizontal");

        if (Input.GetButtonDown("Jump") && IsGrounded())
        {
            body.linearVelocity = new Vector2(body.linearVelocity.x, jumpSpeed);
        }
    }

    private void FixedUpdate()
    {
        body.linearVelocity = new Vector2(
            moveInput * speed,
            body.linearVelocity.y
        );
    }

    private bool IsGrounded()
    {
        return Physics2D.OverlapCircle(
            groundPoint.position,
            groundRadius,
            groundMask
        );
    }
}
~~~

For a new project, the Input System page in this handbook shows how to replace legacy input calls.

## Related

[2d-overview.md](2d-overview.md) · [input.md](input.md) · [debugging.md](debugging.md) · [performance.md](performance.md)

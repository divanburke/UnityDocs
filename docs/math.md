# Unity Math and Movement

Math is everywhere in Unity: movement, aiming, camera control, physics and animation.

## Vector2

Used heavily in 2D.

~~~csharp
Vector2 input = new Vector2(horizontal, vertical);
Vector2 direction = input.normalized;
~~~

Important properties:

| Member | Meaning |
|---|---|
| x / y | Components |
| magnitude | Length |
| sqrMagnitude | Squared length |
| normalized | Unit-length direction |
| zero | (0,0) |
| one | (1,1) |
| up/down/left/right | Cardinal directions |

## Vector3

3D counterpart with x/y/z.

Useful directions:

~~~csharp
transform.forward
transform.right
transform.up
~~~

## normalized vs magnitude

Do not confuse length and direction.

~~~csharp
Vector3 direction = velocity.normalized;
float speed = velocity.magnitude;
~~~

For comparisons where you do not need the actual length:

~~~csharp
if (direction.sqrMagnitude > 0.01f)
{
}
~~~

Squared magnitude avoids a square root.

## Distance

~~~csharp
float distance = Vector3.Distance(a, b);
~~~

Equivalent idea:

~~~csharp
float sqrDistance = (a - b).sqrMagnitude;
~~~

Use squared distance for frequent threshold tests.

## Dot product

Dot answers “how aligned are these directions?”

~~~csharp
float dot = Vector3.Dot(forward, toTarget.normalized);
~~~

Interpretation:

| Dot | Meaning |
|---:|---|
| +1 | Same direction |
| 0 | Perpendicular |
| -1 | Opposite |

## Cross product

Cross creates a vector perpendicular to two 3D vectors.

Useful for:

- left/right tests
- surface orientation
- torque-like direction logic

## Angle

~~~csharp
float angle = Vector3.Angle(a, b);
~~~

For signed yaw around Y, a common pattern is to derive the sign using cross/dot relationships.

## Lerp

~~~csharp
Vector3 result = Vector3.Lerp(a, b, t);
~~~

Important: Lerp uses a normalized t. It is not automatically “move by 5 units per second”.

For constant-speed travel:

~~~csharp
Vector3 result = Vector3.MoveTowards(
    current,
    target,
    speed * Time.deltaTime
);
~~~

## MoveTowards

Best when you want a bounded step and no overshoot.

## SmoothDamp

Useful for critically-damped style smoothing.

~~~csharp
float velocity = 0f;

current = Mathf.SmoothDamp(
    current,
    target,
    ref velocity,
    smoothTime
);
~~~

It requires a velocity state variable.

## Quaternion

Never manually blend Euler angles when Quaternion interpolation is more appropriate.

~~~csharp
transform.rotation = Quaternion.RotateTowards(
    transform.rotation,
    targetRotation,
    turnSpeed * Time.deltaTime
);
~~~

## LookRotation

~~~csharp
Vector3 direction = target.position - transform.position;

if (direction.sqrMagnitude > 0.001f)
{
    transform.rotation = Quaternion.LookRotation(direction);
}
~~~

## 2D angle

For 2D aiming:

~~~csharp
Vector2 direction = target - origin;
float angle = Mathf.Atan2(direction.y, direction.x) * Mathf.Rad2Deg;
transform.rotation = Quaternion.Euler(0f, 0f, angle);
~~~

## Clamp velocity

~~~csharp
if (body.linearVelocity.magnitude > maxSpeed)
{
    body.linearVelocity =
        body.linearVelocity.normalized * maxSpeed;
}
~~~

For frequent physics code, compare squared speed when possible.

## Frame-rate independent movement

Bad:

~~~csharp
transform.position += Vector3.right * speed;
~~~

Better:

~~~csharp
transform.position +=
    Vector3.right * speed * Time.deltaTime;
~~~

Physics body movement should also respect the physics simulation's timing.

## Local vs world directions

~~~csharp
Vector3 worldForward = transform.TransformDirection(Vector3.forward);
~~~

For 2D:

~~~csharp
Vector2 worldRight = transform.right;
~~~

## Common math mistakes

| Problem | Fix |
|---|---|
| Character speeds up on high FPS | Use deltaTime / fixed-step correctly |
| Lerp never reaches exact target | Use MoveTowards or explicit completion |
| Object rotates the long way around | Use Quaternion.RotateTowards or DeltaAngle |
| Tiny input causes jitter | Dead zone / threshold |
| Position comparisons are expensive | Use squared distance |

## Related

[api-core.md](api-core.md) · [2d-physics.md](2d-physics.md) · [3d-physics.md](3d-physics.md)

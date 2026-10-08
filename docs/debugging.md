# Debugging and Troubleshooting

## Start with the symptom

Do not change ten systems at once. Identify:

1. What is wrong?
2. When does it start?
3. Which system owns the behavior?
4. What value first becomes incorrect?

## Console

Use the Console to find:

- compile errors
- exceptions
- warnings
- runtime logs

Fix the first relevant exception before chasing secondary errors.

## Debug.Log

~~~csharp
Debug.Log($"Speed = {speed}", this);
~~~

The context object lets you click from the Console to the object when supported.

## Assertions

~~~csharp
Debug.Assert(body != null, "Rigidbody is required.", this);
~~~

Assertions document assumptions that should always be true.

## Gizmos

Use OnDrawGizmos / OnDrawGizmosSelected for persistent editor visualization.

~~~csharp
private void OnDrawGizmosSelected()
{
    Gizmos.DrawWireSphere(transform.position, 0.5f);
}
~~~

Excellent for:

- attack ranges
- ground checks
- detection areas
- camera bounds
- spawn positions

## Physics visualization

Draw rays and directions:

~~~csharp
Debug.DrawRay(
    transform.position,
    transform.forward * 3f,
    Color.green
);
~~~

Use the Scene view while the game is running to understand queries.

## NullReferenceException

Usually means a reference was not assigned or not initialized.

Check:

- Inspector field
- GetComponent result
- scene object existence
- scene load timing
- conditional destruction

Use a clear guard:

~~~csharp
if (target == null)
{
    Debug.LogError("Target is missing.", this);
    return;
}
~~~

## MissingReferenceException

Unity objects have special destroyed-object behavior. A reference can still exist as a C# wrapper after the underlying Unity object was destroyed.

Do not assume ordinary C# object lifetime rules explain every Unity null-like behavior.

## Physics object falls through floor

Checklist:

~~~text
□ Rigidbody is the correct 2D/3D type
□ Collider is the matching 2D/3D family
□ Collider is enabled
□ Ground collider is enabled
□ Layers are allowed to collide
□ Object is not moved by Transform teleporting
□ Collision detection mode suits speed
□ Physics timestep is sensible
~~~

## Object rotates unexpectedly

Check:

- Rigidbody angular velocity
- constraints
- torque from collisions
- joints
- parent rotation
- scripts writing rotation
- animation/root motion

## Movement jitters

Look for two systems controlling the same property.

Common conflicts:

~~~text
Physics → position
AND
Update → Transform.position
~~~

or:

~~~text
Animator → Transform
AND
Rigidbody → Transform
~~~

Pick one authoritative movement system.

## Input does nothing

For Input System:

~~~text
□ Package installed
□ Action Asset assigned
□ Action Map enabled
□ Binding correct
□ Control scheme correct
□ Callback subscription active
~~~

## Pink materials

Usually indicates a shader/material/render-pipeline compatibility issue.

Check:

- active render pipeline
- shader availability
- material assignment
- package compatibility
- upgrade/conversion tools when changing pipelines

## UI button does nothing

Check:

- EventSystem
- Canvas
- Graphic raycast target
- another UI element blocking clicks
- Button interactable flag
- correct input module

## Animation not changing

Check:

- Animator enabled
- correct Controller assigned
- parameter spelling/types
- transition conditions
- transition exit time
- layer weight
- Avatar/rig setup for 3D

## Compile errors

Fix in dependency order.

Example:

~~~text
Missing type
  ↓
Fix using/reference/package
  ↓
Method signature error
  ↓
Fix call
  ↓
Runtime errors
  ↓
Gameplay tuning
~~~

Do not tune gameplay while the project is still failing to compile.

## Logging a state machine

~~~csharp
Debug.Log(
    $"State={state} Grounded={grounded} Velocity={body.linearVelocity}"
);
~~~

Log state changes rather than every frame whenever possible.

## Reproduction test

Create a tiny scene containing only the failing system.

For example:

~~~text
PhysicsBugTest
├── Player
├── Floor
└── DebugCamera
~~~

A minimal repro often reveals that the “large game” bug was caused by one hidden interaction.

## Git-friendly debugging

When a fix works:

- keep the change small
- commit a known-good state
- describe the reason in the commit message
- avoid mixing unrelated refactors into the fix

## Related

[scripting.md](scripting.md) · [performance.md](performance.md) · [2d-physics.md](2d-physics.md) · [3d-physics.md](3d-physics.md)

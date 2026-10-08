# AI and Navigation

## Navigation model

Unity navigation is built around a navigable surface and agents that can plan movement over it.

Typical pieces:

- NavMesh
- NavMeshAgent
- NavMeshObstacle
- links/connections where supported

## NavMeshAgent

A NavMeshAgent moves an object along a navigation mesh.

Important properties:

| Property | Meaning |
|---|---|
| speed | Maximum travel speed |
| acceleration | Maximum acceleration |
| angularSpeed | Turning rate |
| stoppingDistance | Desired stop distance |
| autoBraking | Deceleration near destination |
| areaMask | Traversable areas |
| isStopped | Pause/resume agent movement |
| pathPending | Path calculation still in progress |
| remainingDistance | Remaining path distance |
| destination | Requested target position |
| velocity | Current agent velocity |

## SetDestination

~~~csharp
using UnityEngine.AI;

[SerializeField] private Transform target;
private NavMeshAgent agent;

private void Awake()
{
    agent = GetComponent<NavMeshAgent>();
}

private void Update()
{
    if (target != null)
    {
        agent.SetDestination(target.position);
    }
}
~~~

SetDestination requests path calculation; the final path may not be immediately ready.

## Following a target

Avoid recalculating needlessly if the target moves by tiny amounts.

~~~csharp
private Vector3 lastTarget;

private void Update()
{
    if (Vector3.Distance(lastTarget, target.position) > 0.5f)
    {
        lastTarget = target.position;
        agent.SetDestination(lastTarget);
    }
}
~~~

## isStopped

~~~csharp
agent.isStopped = true;
~~~

Resume:

~~~csharp
agent.isStopped = false;
~~~

Useful for stun, pause, conversation or scripted moments.

## Manual velocity

NavMeshAgent can also be driven by explicitly setting velocity. This is useful for some player-like movement on a NavMesh, but it changes how the normal destination/avoidance system participates.

## Areas and masks

Use navigation areas to distinguish:

- normal walkable ground
- slow terrain
- special routes
- blocked regions

Then set area masks on agents according to what they can use.

## Obstacles

NavMeshObstacle can affect navigation dynamically. Carve settings determine whether the obstacle creates a hole-like update in the navigation surface.

Use dynamic obstacles sparingly in large scenes.

## AI architecture

A maintainable AI often separates:

~~~text
Perception
   ↓
Decision / State Machine
   ↓
Navigation
   ↓
Animation
   ↓
Presentation
~~~

Do not put every AI behavior in Update as one giant if/else tree.

## State machine example

~~~csharp
public enum EnemyState
{
    Idle,
    Chase,
    Search,
    Return
}
~~~

Switch based on explicit conditions.

## Line of sight

Navigation does not automatically mean visibility.

Combine navigation with a physics ray:

~~~csharp
Vector3 direction = target.position - transform.position;

if (!Physics.Raycast(
    transform.position,
    direction.normalized,
    out RaycastHit hit,
    direction.magnitude,
    sightMask
))
{
    // No blocking collider found.
}
~~~

## Common AI bugs

| Symptom | Likely cause |
|---|---|
| Agent will not move | No valid NavMesh |
| Agent moves but never arrives | Stopping distance / unreachable destination |
| Destination seems delayed | pathPending |
| Agent ignores obstacle | NavMesh configuration |
| Agent jitters | Multiple scripts overwrite position/velocity |
| AI sees through walls | No line-of-sight check |

## Related

[3d-overview.md](3d-overview.md) · [3d-physics.md](3d-physics.md) · [animation.md](animation.md)

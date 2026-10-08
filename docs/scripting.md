# C# Scripting

## MonoBehaviour

Most gameplay scripts derive from <code>MonoBehaviour</code>.

~~~csharp
using UnityEngine;

public class PlayerController : MonoBehaviour
{
    private void Awake()
    {
    }

    private void Start()
    {
    }

    private void Update()
    {
    }
}
~~~

A MonoBehaviour exists as a component attached to a GameObject.

## Lifecycle

### Awake

Use for local initialization and component caching.

~~~csharp
private Rigidbody2D body;

private void Awake()
{
    body = GetComponent<Rigidbody2D>();
}
~~~

### OnEnable / OnDisable

Good for event subscription.

~~~csharp
private void OnEnable()
{
    GameEvents.Died += HandleDeath;
}

private void OnDisable()
{
    GameEvents.Died -= HandleDeath;
}
~~~

### Start

Use when initialization can happen after Awake. Do not use Start as a substitute for dependency design.

### Update

Runs once per rendered frame for enabled behaviours.

Use for:

- reading input
- timers
- non-physics movement logic
- state machines
- UI updates

### FixedUpdate

Use for physics-step logic.

~~~csharp
private void FixedUpdate()
{
    body.AddForce(Vector2.right * 10f);
}
~~~

### LateUpdate

Useful when something should respond after normal Update work.

Classic example: a camera following a player.

## Serialized fields

<code>[SerializeField] private</code> is one of the most useful Unity patterns.

~~~csharp
[SerializeField] private float speed = 5f;
[SerializeField] private Transform visual;
~~~

Benefits:

- private API surface
- editable in Inspector
- values persist through serialization
- clearer ownership

## Public vs private vs SerializeField

| Declaration | Inspector | Other scripts | Typical use |
|---|---:|---:|---|
| public | Usually yes | Yes | Intentional external API |
| private | No | No | Internal state |
| [SerializeField] private | Yes | No | Tunable internal value |
| protected | No | Child classes | Inheritance |

## GetComponent

Use once, then cache.

~~~csharp
private Animator animator;

private void Awake()
{
    animator = GetComponent<Animator>();
}
~~~

Related calls:

~~~csharp
GetComponent<T>();
GetComponentInChildren<T>();
GetComponentInParent<T>();
TryGetComponent<T>(out var component);
~~~

Prefer <code>TryGetComponent</code> when absence is a valid condition.

## Instantiate and Destroy

~~~csharp
GameObject clone = Instantiate(prefab, position, rotation);
Destroy(clone, 2f);
~~~

Destroy removes the object after the current update loop when appropriate. Do not use <code>DestroyImmediate</code> as a normal runtime deletion method.

## Invoke and coroutines

A coroutine pauses its own execution without blocking the whole game thread.

~~~csharp
private IEnumerator Flash()
{
    visual.enabled = false;
    yield return new WaitForSeconds(0.1f);
    visual.enabled = true;
}
~~~

Start it with:

~~~csharp
StartCoroutine(Flash());
~~~

Coroutines are excellent for sequencing. They are not separate CPU threads.

## Events

Use C# events to decouple systems.

~~~csharp
public static event Action PlayerDied;
~~~

Raise:

~~~csharp
PlayerDied?.Invoke();
~~~

Subscribe and unsubscribe in OnEnable / OnDisable.

## Interfaces

Interfaces reduce coupling.

~~~csharp
public interface IDamageable
{
    void TakeDamage(int amount);
}
~~~

Then:

~~~csharp
if (other.TryGetComponent<IDamageable>(out var target))
{
    target.TakeDamage(10);
}
~~~

## ScriptableObject example

~~~csharp
using UnityEngine;

[CreateAssetMenu(menuName = "Game/Character Definition")]
public class CharacterDefinition : ScriptableObject
{
    public float moveSpeed = 6f;
    public int maxHealth = 100;
}
~~~

## Attributes worth knowing

| Attribute | Purpose |
|---|---|
| [SerializeField] | Serialize private field |
| [Header("...")] | Inspector grouping |
| [Tooltip("...")] | Inspector help |
| [Range(min,max)] | Slider for numeric values |
| [Min(value)] | Minimum Inspector value |
| [RequireComponent(typeof(T))] | Automatically require a dependency |
| [CreateAssetMenu] | Create ScriptableObject asset from menu |

## RequireComponent

~~~csharp
[RequireComponent(typeof(Rigidbody2D))]
public class Motor2D : MonoBehaviour
{
}
~~~

This prevents many setup mistakes.

## Time

Useful properties:

| API | Meaning |
|---|---|
| Time.deltaTime | Seconds since previous rendered frame |
| Time.fixedDeltaTime | Physics simulation step size |
| Time.time | Scaled game time |
| Time.unscaledTime | Unscaled real runtime clock |
| Time.timeScale | Global time multiplier |

Use <code>unscaledDeltaTime</code> for pause-resistant timers such as UI transitions.

## Defensive coding

Avoid silent failures.

~~~csharp
if (target == null)
{
    Debug.LogError("Target reference is missing.", this);
    return;
}
~~~

Prefer meaningful field names and explicit assumptions.

## Common scripting traps

- Reading physics input and immediately teleporting a Rigidbody.
- Allocating arrays or strings every frame.
- Searching the entire Scene in Update.
- Forgetting to unsubscribe from static events.
- Calling code on destroyed UnityEngine objects without understanding Unity's special null behavior.
- Mixing scaled and unscaled time.

## Related

[fundamentals.md](fundamentals.md) · [api-core.md](api-core.md) · [debugging.md](debugging.md) · [performance.md](performance.md)

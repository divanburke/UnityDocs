# Unity Performance

Performance work should be measurement-driven. Do not optimize based only on what looks suspicious.

## Three broad budgets

| Budget | Typical limit |
|---|---|
| CPU | Scripts, physics, animation, rendering submission |
| GPU | Shading, overdraw, shadows, post-processing |
| Memory | Textures, meshes, audio, allocations, loaded assets |

## First rule: profile

Use Unity's Profiler and frame/debugging tools to identify the expensive subsystem.

Look for:

- CPU spikes
- GC allocations
- physics time
- rendering time
- batches/draw calls
- shader cost
- memory growth

## Common CPU costs

### Repeated component lookups

Avoid:

~~~csharp
private void Update()
{
    GetComponent<Animator>().SetFloat("Speed", speed);
}
~~~

Prefer:

~~~csharp
private Animator animator;

private void Awake()
{
    animator = GetComponent<Animator>();
}
~~~

### Searching the Scene

Avoid scene-wide searches in Update.

Cache or inject references.

## Garbage collection

Repeated allocations can produce GC spikes.

Watch for:

- new arrays
- LINQ in hot loops
- string concatenation in Update
- boxing
- repeatedly returned arrays from “All” physics queries
- temporary collections

A small allocation once is not the same as an allocation every frame.

## Physics performance

Physics cost rises with:

- many dynamic bodies
- expensive collision shapes
- many contacts
- continuous collision detection
- complex joint networks
- frequent queries

Use appropriate layers so unrelated objects never enter collision consideration.

## Rendering performance

Watch:

- overdraw
- transparent materials
- shadow casters
- material count
- lighting complexity
- particle counts
- texture bandwidth
- model complexity

## Pooling

Pooling avoids repeated Instantiate/Destroy churn.

Basic pattern:

~~~csharp
public GameObject Get()
{
    for (int i = 0; i < pool.Count; i++)
    {
        if (!pool[i].activeSelf)
        {
            pool[i].SetActive(true);
            return pool[i];
        }
    }

    GameObject instance = Instantiate(prefab);
    pool.Add(instance);
    return instance;
}
~~~

Return:

~~~csharp
instance.SetActive(false);
~~~

For larger projects, build a proper pool with initialization, capacity and cleanup rules.

## Object count

Thousands of tiny GameObjects can be expensive even when each script is simple.

If a system naturally has huge counts, consider:

- batching
- ECS/DOTS where appropriate
- data-oriented structures
- GPU instancing
- tilemaps
- pooled objects

Use these when profiling says the normal component model is the bottleneck.

## UI performance

Avoid rebuilding huge UI hierarchies unnecessarily.

Watch:

- layout rebuilds
- content size fitting
- repeated text assignment
- excessive canvas dirties

Split UI into separate canvases when appropriate so small changes do not force unrelated UI to rebuild.

## Update frequency

Not every system needs Update every frame.

Alternatives:

- event-driven
- coroutine
- timer
- slower polling interval
- FixedUpdate for physics
- LateUpdate for camera follow

## Physics queries

Repeated “All” methods can allocate arrays. Prefer lower-allocation approaches for high-frequency systems.

## Texture memory

Texture size has a large memory impact.

A 4K texture is dramatically more data than a 1K texture. Compress and resize assets to what the target device actually needs.

## Audio memory

Uncompressed clips can use significant memory. Import settings should match use:

- short SFX
- music
- voice
- ambient loops

Avoid keeping every long clip fully resident without a reason.

## Performance checklist

~~~text
□ Profile before and after changes
□ Cache component references
□ Avoid per-frame allocations
□ Use layer masks for queries
□ Use pooling for high-churn objects
□ Keep collision shapes simple
□ Limit transparent overdraw
□ Review lights/shadows
□ Review texture sizes
□ Avoid unnecessary Update methods
□ Measure on target hardware
~~~

## Related

[debugging.md](debugging.md) · [2d-physics.md](2d-physics.md) · [3d-rendering.md](3d-rendering.md)

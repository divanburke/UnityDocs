# Unity Fundamentals

## Mental model

Unity is easiest to understand as five layers:

| Layer | Purpose | Typical types |
|---|---|---|
| Scene | Stores a playable world | Scene, SceneManager |
| GameObject | Named entity in a Scene | GameObject |
| Component | Gives an entity behavior/data | Transform, Rigidbody, Collider, scripts |
| Asset | Reusable project data | Prefab, Material, Sprite, AnimationClip |
| Script | Custom behavior | MonoBehaviour, ScriptableObject |

Every GameObject has a Transform. Almost everything else is optional.

## GameObject vs Component

A GameObject is the container. Components provide the functionality.

A player might be:

~~~text
Player (GameObject)
├── Transform
├── Sprite Renderer / Mesh Renderer
├── Rigidbody2D / Rigidbody
├── Collider2D / Collider
└── PlayerController (MonoBehaviour)
~~~

Do not create separate GameObjects just because you need another value. A component is often the better place for behavior or settings.

## Coordinate spaces

| Space | Meaning | Common APIs |
|---|---|---|
| World | Global scene coordinates | Transform.position |
| Local | Relative to parent | Transform.localPosition |
| Screen | Pixels on the game window | Camera.ScreenToWorldPoint |
| Viewport | Normalized 0–1 camera coordinates | Camera.WorldToViewportPoint |
| UI local | Coordinates inside a UI RectTransform | RectTransform.anchoredPosition |

A reliable rule: convert explicitly when crossing spaces.

~~~csharp
Vector3 mouseWorld = Camera.main.ScreenToWorldPoint(
    new Vector3(Input.mousePosition.x, Input.mousePosition.y, 10f)
);
~~~

## Hierarchies

Parenting lets one object follow another.

~~~csharp
child.SetParent(parent, worldPositionStays: false);
~~~

Use false when the child's local transform should become relative to the new parent. Use true when preserving its current world pose matters.

## Tags and layers

**Tags** answer: “What category is this object?”

**Layers** answer: “Which systems should interact with this object?”

Examples:

| Requirement | Use |
|---|---|
| Is this the player? | Tag |
| Should bullets ignore enemies? | Layer collision matrix |
| Should a raycast hit only ground? | LayerMask |
| Is this object an interactable? | Tag or layer, depending on the system |

Prefer <code>CompareTag</code> for tag checks.

~~~csharp
if (other.CompareTag("Player"))
{
    // Player-specific logic.
}
~~~

## Prefabs

A Prefab is a reusable serialized GameObject hierarchy. Use it for enemies, pickups, bullets, UI panels, VFX, props and reusable systems.

Good workflow:

1. Build the object in a Scene.
2. Drag it into the Project window.
3. Edit the Prefab when shared structure changes.
4. Instantiate it when gameplay needs another copy.

~~~csharp
GameObject instance = Instantiate(prefab, spawnPoint.position, spawnPoint.rotation);
~~~

## Serialization basics

Unity serializes supported fields so Inspector values persist.

~~~csharp
[SerializeField] private float moveSpeed = 6f;
[SerializeField] private Transform target;
~~~

This is normally preferable to making implementation details public.

## Update loop at a glance

| Method | Typical purpose |
|---|---|
| Awake | Internal initialization and caching |
| OnEnable | React to being enabled |
| Start | Initialization that can depend on other objects already being initialized |
| Update | Per-render-frame gameplay |
| LateUpdate | Follow-up work after Update, often cameras |
| FixedUpdate | Physics-step work |
| OnDisable | Unsubscribe / cleanup |
| OnDestroy | Final cleanup |

Do not assume every lifecycle callback runs in the exact order you want. Make dependencies explicit when initialization matters.

## Scenes

Scenes are containers for GameObjects and scene-local state. Use SceneManager for transitions.

~~~csharp
using UnityEngine.SceneManagement;

SceneManager.LoadScene("Game");
~~~

For large games, prefer asynchronous loading patterns when a visible loading hitch would be a problem.

## ScriptableObjects

ScriptableObjects store reusable data independently from a scene instance.

Useful for:

- character stats
- item definitions
- weaponless combat values
- dialogue data
- configuration tables
- level settings

They are data assets, not replacements for ordinary scene objects.

## Common mistakes

| Mistake | Better approach |
|---|---|
| Find objects repeatedly every frame | Cache references |
| Put all logic on one giant GameObject | Split responsibilities |
| Use Transform movement on a dynamic Rigidbody | Use physics APIs |
| Use world position when you meant local position | Check the coordinate space |
| Put every value public | Use private + SerializeField |
| Treat tags and layers as interchangeable | Tags identify; layers filter/interact |

## Related

[scripting.md](scripting.md) · [api-core.md](api-core.md) · [content.md](content.md) · [math.md](math.md)

# Core UnityEngine API

This page is a compact reference for the most frequently used core APIs. Signatures are simplified to the overloads most useful in day-to-day gameplay code.

## Object

| Member | What it does |
|---|---|
| Instantiate(...) | Creates a runtime copy |
| Destroy(obj) | Schedules runtime destruction |
| Destroy(obj, t) | Destroys after a delay |
| DontDestroyOnLoad(obj) | Keeps an object when changing Scene |
| name | Object name |
| GetInstanceID() | Returns Unity instance identifier |

## GameObject

| Member | Purpose |
|---|---|
| activeSelf | Local active state |
| activeInHierarchy | Effective active state |
| SetActive(bool) | Enable/disable GameObject |
| CompareTag(string) | Fast tag comparison |
| tag | Current tag |
| layer | Layer index |
| AddComponent<T>() | Adds a component |
| GetComponent<T>() | Finds component on this object |
| TryGetComponent<T>(out T) | Safe component lookup |
| GetComponentInChildren<T>() | Finds child component |
| GetComponentInParent<T>() | Finds parent component |

~~~csharp
if (gameObject.TryGetComponent<Rigidbody2D>(out var body))
{
    body.linearVelocity = Vector2.zero;
}
~~~

## Component

| Member | Purpose |
|---|---|
| gameObject | Owning GameObject |
| transform | Attached Transform |
| GetComponent<T>() | Same-object lookup |
| CompareTag(...) | Check owning object's tag |

## Transform

| Member | Description |
|---|---|
| position | World position |
| rotation | World Quaternion rotation |
| localPosition | Parent-relative position |
| localRotation | Parent-relative rotation |
| localScale | Parent-relative scale |
| eulerAngles | World Euler angles |
| childCount | Direct child count |
| parent | Parent Transform |
| SetParent(...) | Changes parent |
| SetPositionAndRotation(...) | Sets position + rotation |
| TransformPoint(...) | Local point -> world |
| InverseTransformPoint(...) | World point -> local |
| TransformDirection(...) | Local direction -> world direction |
| InverseTransformDirection(...) | World direction -> local |
| Translate(...) | Moves Transform |
| Rotate(...) | Rotates Transform |
| LookAt(...) | Rotates toward a target |

### Movement example

~~~csharp
transform.Translate(Vector3.right * speed * Time.deltaTime, Space.World);
~~~

For a physics body, this is usually not the preferred movement mechanism.

## Time

| Member | Meaning |
|---|---|
| deltaTime | Render-frame interval |
| fixedDeltaTime | Physics interval |
| fixedTime | Physics clock |
| time | Scaled runtime time |
| unscaledTime | Unscaled runtime time |
| timeScale | Global time multiplier |
| realtimeSinceStartup | Real elapsed time |

## Debug

| API | Use |
|---|---|
| Debug.Log | Information |
| Debug.LogWarning | Warning |
| Debug.LogError | Error |
| Debug.Assert | Runtime assumption check |
| Debug.DrawRay | Temporary scene ray visualization |
| Debug.DrawLine | Temporary line visualization |

~~~csharp
Debug.Assert(player != null, "Player reference is required.");
~~~

## Math / Mathf

| API | Purpose |
|---|---|
| Mathf.Abs | Absolute value |
| Mathf.Clamp | Limit a value |
| Mathf.Clamp01 | Limit 0–1 |
| Mathf.Lerp | Linear interpolation |
| Mathf.MoveTowards | Move by a maximum step |
| Mathf.InverseLerp | Convert a value to normalized position |
| Mathf.SmoothStep | Smoothed interpolation |
| Mathf.Repeat | Repeat within a range |
| Mathf.PingPong | Back-and-forth value |
| Mathf.Min / Max | Minimum / maximum |
| Mathf.Sign | Sign of a number |
| Mathf.Sqrt | Square root |
| Mathf.Pow | Exponent |
| Mathf.Sin / Cos / Tan | Trigonometry |
| Mathf.Atan2 | Angle from X/Y |
| Mathf.Deg2Rad / Rad2Deg | Angle conversions |
| Mathf.DeltaAngle | Shortest signed angle difference |
| Mathf.SmoothDamp | Critically damped smoothing |

## Quaternion

| API | Purpose |
|---|---|
| Quaternion.identity | No rotation |
| Quaternion.Euler(x,y,z) | Create Euler rotation |
| Quaternion.LookRotation(direction) | Face a direction in 3D |
| Quaternion.Angle(a,b) | Rotation difference |
| Quaternion.RotateTowards | Rotate by max degrees |
| Quaternion.Slerp | Smooth spherical interpolation |
| Quaternion.Inverse | Reverse a rotation |
| Quaternion.AngleAxis | Rotation around an axis |

## Camera

| Member | Purpose |
|---|---|
| Camera.main | Finds the MainCamera-tagged camera |
| orthographic | Toggles orthographic projection |
| orthographicSize | Orthographic view size |
| fieldOfView | Perspective vertical FOV |
| aspect | Current camera aspect |
| pixelWidth / pixelHeight | Render dimensions |
| ScreenToWorldPoint | Screen -> world |
| WorldToScreenPoint | World -> screen |
| ViewportToWorldPoint | Viewport -> world |
| WorldToViewportPoint | World -> viewport |
| ScreenPointToRay | Screen position -> world ray |

## SceneManager

Requires <code>using UnityEngine.SceneManagement;</code>.

| API | Purpose |
|---|---|
| LoadScene(name/index) | Load a Scene |
| LoadSceneAsync(...) | Async loading |
| UnloadSceneAsync(...) | Unload a Scene |
| GetActiveScene() | Current Scene |
| SetActiveScene(...) | Set active Scene |
| sceneCount | Loaded Scene count |
| GetSceneAt(index) | Get loaded Scene |

~~~csharp
SceneManager.LoadSceneAsync("Level_02");
~~~

## LayerMask

| API | Purpose |
|---|---|
| LayerMask.GetMask("Ground","Enemy") | Build a mask |
| LayerMask.NameToLayer("Ground") | Layer name -> index |
| LayerMask.LayerToName(index) | Layer index -> name |

~~~csharp
int groundMask = LayerMask.GetMask("Ground");
if (Physics2D.Raycast(origin, Vector2.down, 1f, groundMask))
{
}
~~~

## Random

| API | Purpose |
|---|---|
| Random.Range(int,int) | Integer range; max is exclusive |
| Random.Range(float,float) | Float range |
| Random.insideUnitCircle | Random point in unit circle |
| Random.insideUnitSphere | Random point in unit sphere |
| Random.onUnitSphere | Random direction on unit sphere |
| Random.rotation | Random Quaternion |

## Resources to cache

Common component references should generally be acquired once:

~~~csharp
[SerializeField] private Transform target;
private Animator animator;

private void Awake()
{
    animator = GetComponent<Animator>();
}
~~~

See [performance.md](performance.md) for why repeated lookups and allocations matter.

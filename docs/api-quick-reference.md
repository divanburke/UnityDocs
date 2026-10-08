# API Quick Reference

Use this page when you already know what you want and just need the member name.

## Lifecycle

| Method | Timing / use |
|---|---|
| Awake | Local initialization |
| OnEnable | Enabled subscription/activation |
| Start | Initial gameplay setup |
| Update | Every rendered frame |
| FixedUpdate | Physics step |
| LateUpdate | After Update |
| OnDisable | Disable cleanup |
| OnDestroy | Final cleanup |

## GameObject / Component

| Need | API |
|---|---|
| Enable object | SetActive(true) |
| Disable object | SetActive(false) |
| Get component | GetComponent<T>() |
| Safe get | TryGetComponent<T>(out T) |
| Get child component | GetComponentInChildren<T>() |
| Get parent component | GetComponentInParent<T>() |
| Check tag | CompareTag("Tag") |
| Add component | AddComponent<T>() |

## Transform

| Need | API |
|---|---|
| World position | transform.position |
| Local position | transform.localPosition |
| World rotation | transform.rotation |
| Local rotation | transform.localRotation |
| Scale | transform.localScale |
| Parent | transform.parent |
| Set parent | SetParent(...) |
| Move | Translate(...) |
| Rotate | Rotate(...) |
| Face 3D target | LookAt(...) |
| Set position + rotation | SetPositionAndRotation(...) |
| Local point -> world | TransformPoint(...) |
| World point -> local | InverseTransformPoint(...) |

## Runtime object management

| Need | API |
|---|---|
| Create copy | Instantiate(...) |
| Delete | Destroy(...) |
| Keep through Scene load | DontDestroyOnLoad(...) |

## Time

| Need | API |
|---|---|
| Frame delta | Time.deltaTime |
| Physics delta | Time.fixedDeltaTime |
| Scaled time | Time.time |
| Unscaled time | Time.unscaledTime |
| Pause/slow time | Time.timeScale |

## 2D physics

| Need | API |
|---|---|
| Set linear velocity | Rigidbody2D.linearVelocity |
| Set rotation rate | Rigidbody2D.angularVelocity |
| Add force | Rigidbody2D.AddForce(...) |
| Add torque | Rigidbody2D.AddTorque(...) |
| Move body | Rigidbody2D.MovePosition(...) |
| Rotate body | Rigidbody2D.MoveRotation(...) |
| Ray query | Physics2D.Raycast(...) |
| All ray hits | Physics2D.RaycastAll(...) |
| Circle query | Physics2D.OverlapCircle(...) |
| All circle hits | Physics2D.OverlapCircleAll(...) |
| Capsule query | Physics2D.OverlapCapsule(...) |
| Circle sweep | Physics2D.CircleCast(...) |

## 3D physics

| Need | API |
|---|---|
| Set linear velocity | Rigidbody.linearVelocity |
| Add force | Rigidbody.AddForce(...) |
| Add torque | Rigidbody.AddTorque(...) |
| Move body | Rigidbody.MovePosition(...) |
| Rotate body | Rigidbody.MoveRotation(...) |
| Ray query | Physics.Raycast(...) |
| All ray hits | Physics.RaycastAll(...) |
| Sphere test | Physics.OverlapSphere(...) |
| Box test | Physics.OverlapBox(...) |
| Sphere sweep | Physics.SphereCast(...) |
| Capsule sweep | Physics.CapsuleCast(...) |

## Collision callbacks

| Physics | Solid contact enter | Trigger enter |
|---|---|---|
| 2D | OnCollisionEnter2D | OnTriggerEnter2D |
| 3D | OnCollisionEnter | OnTriggerEnter |

Matching stay/exit forms:

~~~text
OnCollisionStay2D / OnCollisionExit2D
OnTriggerStay2D   / OnTriggerExit2D

OnCollisionStay   / OnCollisionExit
OnTriggerStay     / OnTriggerExit
~~~

## Animation

| Need | API |
|---|---|
| Bool parameter | Animator.SetBool(...) |
| Float parameter | Animator.SetFloat(...) |
| Int parameter | Animator.SetInteger(...) |
| Trigger | Animator.SetTrigger(...) |
| Clear trigger | Animator.ResetTrigger(...) |
| Play state | Animator.Play(...) |
| Blend state | Animator.CrossFade(...) |
| Hash parameter | Animator.StringToHash(...) |

## Scene management

| Need | API |
|---|---|
| Load | SceneManager.LoadScene(...) |
| Async load | SceneManager.LoadSceneAsync(...) |
| Unload | SceneManager.UnloadSceneAsync(...) |
| Current Scene | SceneManager.GetActiveScene() |
| Set active | SceneManager.SetActiveScene(...) |
| Loaded count | SceneManager.sceneCount |

## Math

| Need | API |
|---|---|
| Length | vector.magnitude |
| Cheap length comparison | vector.sqrMagnitude |
| Direction | vector.normalized |
| Distance | Vector2/3.Distance |
| Alignment | Vector2/3.Dot |
| 3D perpendicular vector | Vector3.Cross |
| Angle | Vector2/3.Angle |
| Interpolate | Lerp |
| Constant-step approach | MoveTowards |
| Damp | SmoothDamp |
| Quaternion turn | Quaternion.RotateTowards |
| Euler quaternion | Quaternion.Euler |
| Aim rotation | Quaternion.LookRotation |
| Clamp number | Mathf.Clamp |
| Clamp 0..1 | Mathf.Clamp01 |
| Smooth scalar | Mathf.SmoothDamp |

## Camera

| Need | API |
|---|---|
| Main camera | Camera.main |
| Screen -> world | ScreenToWorldPoint(...) |
| World -> screen | WorldToScreenPoint(...) |
| Screen -> ray | ScreenPointToRay(...) |
| World -> viewport | WorldToViewportPoint(...) |
| Viewport -> world | ViewportToWorldPoint(...) |

## UI

| Need | Typical API |
|---|---|
| Button click | Button.onClick |
| Image progress | Image.fillAmount |
| Enable UI | GameObject.SetActive |
| Text | TMP_Text.text |
| Query Toolkit element | root.Q<T>(...) |

## Audio

| Need | API |
|---|---|
| Play source | AudioSource.Play() |
| Pause | AudioSource.Pause() |
| Resume | AudioSource.UnPause() |
| Stop | AudioSource.Stop() |
| One-shot | AudioSource.PlayOneShot(...) |

## Navigation

| Need | API |
|---|---|
| Set target | NavMeshAgent.SetDestination(...) |
| Stop | NavMeshAgent.isStopped |
| Current velocity | NavMeshAgent.velocity |
| Pending path | NavMeshAgent.pathPending |
| Destination | NavMeshAgent.destination |
| Remaining distance | NavMeshAgent.remainingDistance |

## Debugging

| Need | API |
|---|---|
| Log | Debug.Log(...) |
| Warning | Debug.LogWarning(...) |
| Error | Debug.LogError(...) |
| Assertion | Debug.Assert(...) |
| Scene ray | Debug.DrawRay(...) |
| Scene line | Debug.DrawLine(...) |
| Editor gizmo | OnDrawGizmos / OnDrawGizmosSelected |

## Important naming trap

Do not blindly copy old tutorial code.

Unity 6 examples may use:

~~~csharp
Rigidbody.linearVelocity
Rigidbody2D.linearVelocity
~~~

instead of older examples such as:

~~~text
Rigidbody.velocity
Rigidbody2D.velocity
~~~

Check your installed Unity version and package version when a method/property does not exist.

## Related

[api-core.md](api-core.md) · [2d-physics.md](2d-physics.md) · [3d-physics.md](3d-physics.md)

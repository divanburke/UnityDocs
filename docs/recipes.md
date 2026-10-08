# Unity Code Recipes

These are practical building blocks rather than complete game systems.

## 1. Move a 2D character

~~~csharp
using UnityEngine;

[RequireComponent(typeof(Rigidbody2D))]
public class Move2D : MonoBehaviour
{
    [SerializeField] private float speed = 6f;

    private Rigidbody2D body;
    private float input;

    private void Awake()
    {
        body = GetComponent<Rigidbody2D>();
    }

    private void Update()
    {
        input = Input.GetAxisRaw("Horizontal");
    }

    private void FixedUpdate()
    {
        body.linearVelocity = new Vector2(
            input * speed,
            body.linearVelocity.y
        );
    }
}
~~~

For new projects using Input System, replace input reading with an InputAction.

## 2. Jump once from the ground

~~~csharp
private bool IsGrounded()
{
    return Physics2D.OverlapCircle(
        groundPoint.position,
        groundRadius,
        groundMask
    );
}

private void Jump()
{
    if (!IsGrounded())
    {
        return;
    }

    body.linearVelocity = new Vector2(
        body.linearVelocity.x,
        jumpSpeed
    );
}
~~~

## 3. Hit detection with a cooldown

Use a timestamp rather than a boolean when repeated hits must be limited.

~~~csharp
[SerializeField] private float hitCooldown = 0.25f;
private float nextHitTime;

private bool CanHit()
{
    return Time.time >= nextHitTime;
}

private void RegisterHit()
{
    nextHitTime = Time.time + hitCooldown;
}
~~~

This pattern keeps the cooldown independent of frame rate.

## 4. Smooth camera follow

~~~csharp
using UnityEngine;

public class CameraFollow : MonoBehaviour
{
    [SerializeField] private Transform target;
    [SerializeField] private float smoothTime = 0.15f;

    private Vector3 velocity;

    private void LateUpdate()
    {
        Vector3 desired = target.position;
        desired.z = transform.position.z;

        transform.position = Vector3.SmoothDamp(
            transform.position,
            desired,
            ref velocity,
            smoothTime
        );
    }
}
~~~

## 5. Face movement direction

~~~csharp
private void Face(float direction)
{
    if (Mathf.Abs(direction) < 0.01f)
    {
        return;
    }

    visual.flipX = direction < 0f;
}
~~~

For 3D, use a rotation rather than SpriteRenderer.flipX.

## 6. Spawn a Prefab

~~~csharp
[SerializeField] private GameObject prefab;
[SerializeField] private Transform spawnPoint;

private void Spawn()
{
    Instantiate(
        prefab,
        spawnPoint.position,
        spawnPoint.rotation
    );
}
~~~

## 7. Destroy after a delay

~~~csharp
Destroy(gameObject, lifetime);
~~~

Use pooling instead when thousands of short-lived objects are created repeatedly.

## 8. Trigger pickup

~~~csharp
private void OnTriggerEnter2D(Collider2D other)
{
    if (!other.CompareTag("Player"))
    {
        return;
    }

    Collect();
    Destroy(gameObject);
}

private void Collect()
{
    // Add item to inventory.
}
~~~

## 9. Raycast interaction

~~~csharp
if (Physics.Raycast(
    ray,
    out RaycastHit hit,
    interactionDistance,
    interactionMask
))
{
    if (hit.collider.TryGetComponent<IInteractable>(
        out var interactable))
    {
        interactable.Interact();
    }
}
~~~

## 10. Interface-based damage

~~~csharp
public interface IDamageable
{
    void TakeDamage(int amount);
}
~~~

Apply:

~~~csharp
if (target.TryGetComponent<IDamageable>(
    out var damageable))
{
    damageable.TakeDamage(10);
}
~~~

## 11. Temporary invulnerability

~~~csharp
[SerializeField] private float invulnerability = 0.75f;
private float invulnerableUntil;

public bool IsInvulnerable =>
    Time.time < invulnerableUntil;

public void StartInvulnerability()
{
    invulnerableUntil = Time.time + invulnerability;
}
~~~

## 12. Simple health

~~~csharp
using UnityEngine;

public class Health : MonoBehaviour
{
    [SerializeField] private int maxHealth = 100;

    public int Current { get; private set; }

    private void Awake()
    {
        Current = maxHealth;
    }

    public void TakeDamage(int amount)
    {
        if (amount <= 0)
        {
            return;
        }

        Current = Mathf.Max(0, Current - amount);

        if (Current == 0)
        {
            Die();
        }
    }

    private void Die()
    {
        // Death logic.
    }
}
~~~

## 13. Cooldown timer

~~~csharp
[SerializeField] private float cooldown = 1f;
private float readyAt;

public bool Ready => Time.time >= readyAt;

public void Use()
{
    if (!Ready)
    {
        return;
    }

    readyAt = Time.time + cooldown;
    Execute();
}
~~~

## 14. Toggle a menu

~~~csharp
[SerializeField] private GameObject pauseMenu;

public void TogglePauseMenu()
{
    pauseMenu.SetActive(!pauseMenu.activeSelf);
}
~~~

For actual game pause:

~~~csharp
Time.timeScale = 0f;
~~~

Remember that scaled timers/coroutines can pause too. Use unscaled time for UI that must continue animating.

## 15. Load next scene

~~~csharp
using UnityEngine.SceneManagement;

public void LoadScene(string sceneName)
{
    SceneManager.LoadSceneAsync(sceneName);
}
~~~

## 16. Animator movement

~~~csharp
private static readonly int SpeedId =
    Animator.StringToHash("Speed");

private void UpdateAnimator()
{
    animator.SetFloat(
        SpeedId,
        new Vector3(body.linearVelocity.x, 0f, 0f).magnitude
    );
}
~~~

## 17. 2D area attack

~~~csharp
[SerializeField] private Transform attackPoint;
[SerializeField] private float radius = 0.75f;
[SerializeField] private LayerMask targetMask;

public void Attack()
{
    Collider2D[] hits = Physics2D.OverlapCircleAll(
        attackPoint.position,
        radius,
        targetMask
    );

    foreach (Collider2D hit in hits)
    {
        if (hit.TryGetComponent<IDamageable>(
            out var damageable))
        {
            damageable.TakeDamage(10);
        }
    }
}
~~~

For very frequent attacks, use a lower-allocation query strategy.

## 18. Draw a detection range

~~~csharp
private void OnDrawGizmosSelected()
{
    Gizmos.DrawWireSphere(
        transform.position,
        detectionRadius
    );
}
~~~

## 19. 3D mouse click onto ground

~~~csharp
Ray ray = Camera.main.ScreenPointToRay(Input.mousePosition);

if (Physics.Raycast(
    ray,
    out RaycastHit hit,
    500f,
    groundMask
))
{
    MoveTarget(hit.point);
}
~~~

## 20. Smooth numeric value

~~~csharp
float velocity = 0f;

current = Mathf.SmoothDamp(
    current,
    target,
    ref velocity,
    0.2f
);
~~~

## 21. Clamp health bar

~~~csharp
healthFill.fillAmount =
    Mathf.Clamp01((float)currentHealth / maxHealth);
~~~

## 22. Require a component dependency

~~~csharp
[RequireComponent(typeof(Collider2D))]
public class Pickup : MonoBehaviour
{
}
~~~

## 23. Safe initialization

~~~csharp
private void Awake()
{
    if (!TryGetComponent<Rigidbody2D>(out body))
    {
        Debug.LogError(
            "Rigidbody2D required.",
            this
        );
        enabled = false;
    }
}
~~~

## 24. Event subscription

~~~csharp
private void OnEnable()
{
    GameEvents.LevelStarted += OnLevelStarted;
}

private void OnDisable()
{
    GameEvents.LevelStarted -= OnLevelStarted;
}

private void OnLevelStarted()
{
}
~~~

## Recipe rule

When a small recipe works, turn it into a named component with a single responsibility. Do not keep copying the same anonymous snippet into ten controllers.

## Related

[api-quick-reference.md](api-quick-reference.md) · [scripting.md](scripting.md) · [debugging.md](debugging.md)

# Scenes, Prefabs and Assets

## Scene

A Scene stores a set of GameObjects and scene-local serialized state.

Use separate scenes for:

- boot/loading
- menu
- levels
- gameplay arenas
- test environments

## Scene loading

~~~csharp
using UnityEngine.SceneManagement;

public void LoadGame()
{
    SceneManager.LoadSceneAsync("Game");
}
~~~

Prefer async loading where loading pauses would be visible.

## DontDestroyOnLoad

~~~csharp
DontDestroyOnLoad(gameObject);
~~~

Good candidates:

- audio manager
- save system
- global game state

Do not casually mark every manager persistent. Duplicates across scene loads are a common bug.

## Prefabs

Use Prefabs when an object should be instantiated or edited as a reusable unit.

Good Prefab candidates:

- player
- enemy
- projectile
- pickup
- effect
- UI panel
- environment prop

## Instantiate

~~~csharp
[SerializeField] private GameObject enemyPrefab;

private void Spawn(Vector3 position)
{
    Instantiate(enemyPrefab, position, Quaternion.identity);
}
~~~

For high-volume objects, consider pooling instead of constantly creating/destroying them.

## Prefab variants

Prefab Variants are useful when several objects share a common base.

Example:

~~~text
EnemyBase
├── FastEnemy (Variant)
├── HeavyEnemy (Variant)
└── FlyingEnemy (Variant)
~~~

Keep the base Prefab focused on shared structure.

## ScriptableObject data

ScriptableObjects are useful for definitions rather than per-instance runtime state.

Example:

~~~csharp
[CreateAssetMenu(menuName = "Game/Item")]
public class ItemDefinition : ScriptableObject
{
    public string displayName;
    public Sprite icon;
    public int value;
}
~~~

## Resources

The Resources folder allows runtime loading by path, but it has tradeoffs:

- implicit dependency discovery
- memory management considerations
- naming/path coupling

Do not use Resources as the automatic solution for every asset system.

## Addressables

Addressables provide a managed way to load assets asynchronously by address/key and can support remote/content workflows. They are appropriate for projects where asset lifetime, memory and distribution need a more deliberate model.

## Asset naming

A useful naming style:

| Asset | Example |
|---|---|
| Scene | SCN_MainMenu |
| Prefab | PF_Player |
| Material | MAT_Ground |
| Sprite | SPR_Player_Idle |
| Animation | AN_Player_Run |
| Audio | SFX_Jump |
| Script | PlayerController |
| ScriptableObject | ItemDefinition |

Use whatever convention the project adopts; consistency matters more than the exact prefix.

## Folder layout

A scalable example:

~~~text
Assets/
├── Art/
├── Audio/
├── Materials/
├── Prefabs/
├── Scenes/
├── Scripts/
│   ├── Gameplay/
│   ├── Systems/
│   └── UI/
├── Settings/
└── Data/
~~~

## Asset references

Direct serialized references are usually safer than magic string paths when the asset relationship is fixed.

~~~csharp
[SerializeField] private AudioClip jumpClip;
~~~

## Scene references vs asset references

A scene reference points to an instantiated object in the current runtime world.

An asset reference points to a reusable project asset.

Do not expect a prefab asset to behave like the scene instance that was instantiated from it.

## Common content mistakes

| Mistake | Better |
|---|---|
| Duplicate the same prefab manually | Instantiate the Prefab |
| Huge “everything” scene | Split by gameplay purpose |
| Global singleton everywhere | Use explicit services where practical |
| Magic asset paths everywhere | Serialized references or an asset system |
| Runtime state stored directly in ScriptableObject | Separate definition from per-run state |

## Related

[fundamentals.md](fundamentals.md) · [scripting.md](scripting.md) · [performance.md](performance.md)

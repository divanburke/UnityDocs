# Unity Editor and Project Setup

This page covers the project-level decisions that prevent many later problems.

## Recommended first setup

After creating a project:

1. Confirm the Unity Editor version.
2. Confirm the active render pipeline.
3. Confirm required packages.
4. Create a folder structure.
5. Decide tag/layer conventions.
6. Create a test Scene.
7. Configure input.
8. Add the first build Scene.
9. Commit the clean project state to Git.

## Folder structure

~~~text
Assets/
├── Art/
│   ├── Sprites/
│   ├── Models/
│   └── Materials/
├── Audio/
├── Data/
├── Prefabs/
├── Scenes/
│   ├── Main/
│   └── Tests/
├── Scripts/
│   ├── Gameplay/
│   ├── Systems/
│   └── UI/
├── Settings/
└── UI/
~~~

The exact names do not matter. Predictability does.

## Scene workflow

Keep dedicated test scenes for difficult systems.

Examples:

~~~text
SCN_MainMenu
SCN_Game
SCN_TestPhysics2D
SCN_TestCharacter
SCN_TestUI
~~~

This is especially valuable when debugging physics: you can remove unrelated systems and see whether the body itself behaves correctly.

## Tags

Use tags for identity.

Examples:

| Tag | Meaning |
|---|---|
| Player | Main player |
| Enemy | Enemy identity |
| Ground | Ground identity |
| Pickup | Collectable |
| MainCamera | Main gameplay camera |

Do not create dozens of tags when a layer or explicit reference is a better fit.

## Layers

Use layers for filtering.

Example conceptual set:

~~~text
Default
Player
Enemy
Ground
Environment
Projectile-like effects
UI / special camera layers
~~~

Then configure:

- Physics collision matrix
- Camera culling masks
- Physics query masks

## LayerMask in scripts

~~~csharp
[SerializeField] private LayerMask groundMask;

private bool IsGrounded()
{
    return Physics.Raycast(
        transform.position,
        Vector3.down,
        1.1f,
        groundMask
    );
}
~~~

This is better than embedding numeric layer values in code.

## Input setup

For new projects, configure the Input System package and create an Input Action Asset.

Start with action maps based on gameplay state:

~~~text
Player
UI
Vehicle
Menu
~~~

Do not make every physical key a separate gameplay action.

## Project settings worth checking

| Area | Why |
|---|---|
| Player | Application and platform settings |
| Quality | Rendering quality tiers |
| Physics | Global 3D physics behavior |
| Physics 2D | Global 2D physics behavior |
| Time | Time scale related settings |
| Tags and Layers | Object categorization/filtering |
| Input | Input package/project behavior |
| Graphics | Render pipeline/assets |
| Build Profiles / scenes | What is included in builds |

Exact names and panels can change between Unity releases.

## Physics settings

Global physics configuration affects every body in the corresponding physics world.

When a physics game behaves strangely, inspect both:

- per-Rigidbody settings
- global Physics / Physics 2D settings

## Build scene checklist

Before creating a standalone build:

~~~text
□ Main entry Scene is included
□ Required gameplay Scenes are included
□ Scene names are correct
□ Input package works in Player
□ Required graphics pipeline assets are included
□ Platform is selected
□ Development logging is reviewed
~~~

## Enter Play Mode carefully

Common sources of confusion:

- static data survives in the editor but not in a real build
- domain/scene reload options change initialization expectations
- editor-only APIs accidentally appear in runtime code
- entering Play Mode from a different test scene creates a false impression of startup behavior

Write initialization code that is correct regardless of which Scene you start from.

## Prefab workflow

When a reusable object is correct:

1. Make it a Prefab.
2. Reference the Prefab from scripts or another Prefab.
3. Instantiate it where required.
4. Keep per-instance runtime state on scene instances.

## Git workflow

A good Unity project should keep source assets and project configuration under version control while ignoring generated/cache directories.

Typical generated directories are not hand-edited source:

~~~text
Library/
Temp/
Logs/
obj/
Build/
~~~

Use the standard Unity .gitignore appropriate for your project's Unity version and tooling.

## Safe change workflow

For a tricky change:

~~~text
Known-good commit
       ↓
One focused change
       ↓
Run/test
       ↓
Commit
       ↓
Next change
~~~

This makes physics and controller tuning much easier to reverse.

## Package management

Packages are part of the project's dependency surface. When a class cannot be found:

1. confirm the package is installed;
2. confirm the namespace;
3. confirm the API matches the package version;
4. check whether the package is optional or editor-only.

Do not paste package code into a project before understanding who owns the dependency.

## Common editor problems

| Symptom | Check first |
|---|---|
| Script won't compile | Console first error |
| Component missing | Package/namespace/assembly |
| Scene changes lost | Scene dirty state |
| Prefab changes confusing | Overrides vs asset root |
| Input missing | Package + action map |
| Material pink | Render pipeline/shader |
| Build works differently | Editor-only behavior / settings |

## Related

[fundamentals.md](fundamentals.md) · [debugging.md](debugging.md) · [content.md](content.md) · [input.md](input.md)

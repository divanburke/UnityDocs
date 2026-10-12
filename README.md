# Unity Docs — Practical Unity 2D + 3D Reference

A browsable, self-contained Unity development reference designed to reduce the need for repeated web searches.

## What this repository is

This is a **working developer handbook**, not a copy of Unity's documentation. It focuses on:

- what Unity systems do
- which component/function to use
- exact C# signatures and useful overloads
- Inspector setup
- common patterns
- 2D and 3D differences
- performance implications
- debugging and failure modes
- complete copy/paste-ready examples

The baseline is **Unity 6 / current Unity 6 API naming**. Older Unity versions can differ, especially around physics APIs and packages.

## Start here

| Need | Read |
|---|---|
| Browse the full documentation map | [Documentation Index](docs/README.md) |
| Learn the overall structure | [Unity Fundamentals](docs/getting-started/fundamentals.md) |
| C# scripts and lifecycle | [Scripting](docs/scripting/scripting.md) |
| Core Unity API | [Core API](docs/api/core.md) |
| Inspector component lookup | [Component Catalog](docs/api/components.md) |
| Unity Editor / project setup | [Editor Workflow](docs/getting-started/editor-workflow.md) |
| Build a 2D game | [2D Overview](docs/2d/overview.md) |
| 2D physics / collision | [2D Physics](docs/2d/physics.md) |
| Sprites / Tilemaps | [2D Graphics](docs/2d/graphics.md) |
| Build a 3D game | [3D Overview](docs/3d/overview.md) |
| 3D physics / collision | [3D Physics](docs/3d/physics.md) |
| Rendering / cameras / materials | [3D Rendering](docs/3d/rendering.md) |
| Input | [Input](docs/systems/input.md) |
| UI | [UI](docs/systems/ui.md) |
| Scenes / prefabs / assets | [Content](docs/systems/content.md) |
| Animation | [Animation](docs/systems/animation.md) |
| AI / navigation | [AI & Navigation](docs/systems/ai-navigation.md) |
| Audio | [Audio](docs/systems/audio.md) |
| Math / vectors / rotations | [Math](docs/systems/math.md) |
| Performance | [Performance](docs/quality/performance.md) |
| Debugging | [Debugging](docs/quality/debugging.md) |
| Common recipes | [Recipes](docs/recipes/recipes.md) |
| Quick API lookup | [API Quick Reference](docs/api/quick-reference.md) |

## How to use these docs

Each topic follows the same pattern:

1. **What it is**
2. **When to use it**
3. **Inspector setup**
4. **Important properties**
5. **Important methods**
6. **Minimal code**
7. **Production pattern**
8. **Common mistakes**
9. **2D vs 3D differences**
10. **Related topics**

### Code notation

Examples use normal Unity C# scripts. Code blocks are intentionally complete enough to adapt directly.

~~~csharp
using UnityEngine;

public class Example : MonoBehaviour
{
    private void Update()
    {
        // Per-frame game logic goes here.
    }
}
~~~

## Fast lookup rules

**Need to move a Transform?** Use the Transform component.

**Need physical movement?** Use Rigidbody / Rigidbody2D and the appropriate physics update.

**Need collision detection?** Use the matching Collider / Collider2D plus collision or trigger callbacks, or physics queries such as raycasts and overlaps.

**Need an object in the scene?** Use a GameObject with components.

**Need to locate another component?** Prefer cached references and Inspector references over repeatedly searching the scene.

**Need an object that can be reused?** Make a Prefab.

**Need a value that should be editable in Inspector but does not need to be public API?** Use [SerializeField] private.

## Important version note

Unity's APIs evolve. This repository intentionally documents a Unity 6-oriented workflow. Always check your project's installed package version before using package-specific APIs such as Input System, AI Navigation, Cinemachine, or UI Toolkit.

## Official source

The material is based on concepts and APIs documented by Unity. It is written independently and paraphrased so this repository remains a practical reference rather than a mirror of Unity's documentation.

Primary reference: https://docs.unity3d.com/

---

## Repository structure

The documentation is grouped by subject so related topics stay together:

```text
docs/
├── README.md                  # Topic index
├── getting-started/           # Fundamentals and project setup
├── 2d/                        # 2D game development, physics, graphics
├── 3d/                        # 3D game development, physics, rendering
├── scripting/                 # C# scripts and component design
├── api/                       # Core API, component catalog, quick reference
├── systems/                   # Input, UI, animation, audio, AI, assets, math
├── quality/                   # Debugging and performance
└── recipes/                   # Complete reusable patterns
```

Open [the documentation index](docs/README.md) for a topic-by-topic guide.

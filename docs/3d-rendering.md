# 3D Rendering, Cameras and Materials

## Rendering pipeline

Unity projects render through a configured render pipeline. Common projects use:

- Built-in Render Pipeline
- Universal Render Pipeline (URP)
- High Definition Render Pipeline (HDRP)

Shader, lighting and post-processing details depend on the active pipeline and installed packages.

## Main render components

| Component | Job |
|---|---|
| Camera | Defines the view |
| MeshFilter | Supplies mesh |
| MeshRenderer | Draws mesh |
| SkinnedMeshRenderer | Draws deforming character mesh |
| Light | Illuminates |
| Material | Supplies shader parameters |
| Reflection/probe systems | Provide environment information |
| ParticleSystem | Visual effects |

## Camera coordinate conversion

### World to screen

~~~csharp
Vector3 screen = Camera.main.WorldToScreenPoint(worldPosition);
~~~

### Screen to world

For perspective cameras, the input Z value represents distance from the camera.

~~~csharp
Vector3 screen = new Vector3(
    Input.mousePosition.x,
    Input.mousePosition.y,
    10f
);

Vector3 world = Camera.main.ScreenToWorldPoint(screen);
~~~

For mouse picking on arbitrary surfaces, a ray plus Physics.Raycast is usually more reliable than guessing a Z distance.

## Mouse click to world surface

~~~csharp
Ray ray = Camera.main.ScreenPointToRay(Input.mousePosition);

if (Physics.Raycast(ray, out RaycastHit hit, 500f, groundMask))
{
    Vector3 point = hit.point;
}
~~~

## Field of view

Perspective FOV changes how much of the world is visible vertically.

| FOV choice | Typical visual result |
|---|---|
| Very wide | More dramatic perspective |
| Medium | General gameplay |
| Very narrow | Telephoto/tunnel-like view |

## Clipping planes

| Setting | Meaning |
|---|---|
| Near Clip Plane | Closest visible depth |
| Far Clip Plane | Farthest visible depth |

Set them to values appropriate for the scene scale. Excessively large ranges can reduce depth precision and visibility efficiency.

## Renderer and material basics

A MeshRenderer references one or more Materials. Each Material uses a Shader plus property values.

~~~csharp
Renderer renderer = GetComponent<Renderer>();
renderer.material.SetFloat("_Metallic", 0.2f);
~~~

Shader property names are pipeline/shader dependent. _Metallic and _BaseColor are common conventions but are not universal.

## material vs sharedMaterial

renderer.material can create a per-renderer material instance.

renderer.sharedMaterial refers to the shared material reference and should not be modified casually at runtime because every user of that shared material can be affected.

## MaterialPropertyBlock

Use a MaterialPropertyBlock when you want per-renderer values without creating a separate material for every object.

~~~csharp
using UnityEngine;

public class PerRendererColor : MonoBehaviour
{
    private static readonly int BaseColorId =
        Shader.PropertyToID("_BaseColor");

    [SerializeField] private Renderer targetRenderer;
    [SerializeField] private Color color = Color.white;

    private MaterialPropertyBlock block;

    private void Awake()
    {
        block = new MaterialPropertyBlock();
    }

    private void Apply()
    {
        targetRenderer.GetPropertyBlock(block);
        block.SetColor(BaseColorId, color);
        targetRenderer.SetPropertyBlock(block);
    }
}
~~~

## Lighting

Main light types:

| Light | Use |
|---|---|
| Directional | Sun / global direction |
| Point | Local spherical source |
| Spot | Cone-shaped source |
| Area | Large source / softer lighting where supported |

Shadows can be expensive. Cost grows with shadow map work, resolution, number of shadowing lights and scene complexity.

## Transparent materials

Transparency can create high overdraw because many pixels may be shaded repeatedly. Prefer opaque materials for solid surfaces.

## Draw calls and batching

Rendering cost depends on:

- renderer count
- material count
- state changes
- shader complexity
- transparency
- lighting
- shadows
- texture bandwidth

Do not assume low polygon count automatically means fast.

## Culling

Unity uses camera visibility and scene data to avoid unnecessary rendering. Frustum culling is the basic case; more advanced visibility systems can further reduce work in large environments.

## LOD

Level of Detail changes the representation based on distance.

~~~text
Near    -> High detail
Medium  -> Medium detail
Far     -> Low detail / simplified representation
~~~

LOD works best when differences are visually small at the switch distances.

## Common rendering problems

| Symptom | Common cause |
|---|---|
| Object invisible | Renderer disabled, layer/culling, clipping |
| Pink material | Shader/render-pipeline mismatch |
| Surface flickers | Z-fighting |
| Scene too dark | Light/exposure/material problem |
| Effects tank FPS | Overdraw, particles, post-processing |
| Changing one object changes others | Shared material was modified |

## Related

[3d-overview.md](3d-overview.md) · [performance.md](performance.md) · [debugging.md](debugging.md)

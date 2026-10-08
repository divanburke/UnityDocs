# Unity UI Reference

Unity projects commonly use two UI approaches: Canvas-based UI and UI Toolkit. Choose based on project needs and package configuration.

## Canvas UI

A Canvas is the root of classic runtime UI.

Common hierarchy:

~~~text
Canvas
├── Panel
│   ├── Title
│   ├── HealthBar
│   └── Button
└── PauseMenu
~~~

## Canvas render modes

| Mode | Use |
|---|---|
| Screen Space - Overlay | HUD fixed to screen |
| Screen Space - Camera | UI associated with camera |
| World Space | UI exists in the 3D world |

## RectTransform

UI objects use RectTransform rather than ordinary Transform behavior.

Important fields:

| Property | Purpose |
|---|---|
| anchorMin / anchorMax | Parent-relative anchors |
| anchoredPosition | Position relative to anchors |
| sizeDelta | Size relative to anchors |
| pivot | Internal reference point |
| localScale | Scale |

Anchors are the key to responsive layout.

## Text

Depending on project packages, common text components include TextMeshPro components.

Example:

~~~csharp
using TMPro;

[SerializeField] private TMP_Text scoreText;

private void SetScore(int score)
{
    scoreText.text = score.ToString();
}
~~~

## Button

~~~csharp
using UnityEngine;
using UnityEngine.UI;

public class Menu : MonoBehaviour
{
    [SerializeField] private Button playButton;

    private void OnEnable()
    {
        playButton.onClick.AddListener(Play);
    }

    private void OnDisable()
    {
        playButton.onClick.RemoveListener(Play);
    }

    private void Play()
    {
        Debug.Log("Play");
    }
}
~~~

## Images and fill bars

Image types can be used for health, cooldowns and progress.

~~~csharp
[SerializeField] private Image healthFill;

private void SetHealth(float normalized)
{
    healthFill.fillAmount = Mathf.Clamp01(normalized);
}
~~~

## Layout Groups

Useful components:

- HorizontalLayoutGroup
- VerticalLayoutGroup
- GridLayoutGroup
- ContentSizeFitter
- LayoutElement

Avoid stacking layout components without understanding who controls size. Parent/child layout authority matters.

## Scroll views

Typical:

~~~text
Scroll View
├── Viewport
│   └── Content
│       ├── Item
│       ├── Item
│       └── Item
~~~

The Content RectTransform normally owns the changing content size.

## EventSystem

Interactive UI needs an EventSystem configured for the active input approach.

Common symptoms of a broken event setup:

- buttons do nothing
- pointer events fail
- navigation does not work

## World-space UI

Useful for:

- health bars above characters
- nameplates
- interaction labels
- diegetic screens

Keep world-space canvas scaling intentional.

## UI Toolkit

UI Toolkit is based around:

- VisualElement
- UXML
- USS
- UI Document
- UI events

It is especially attractive for editor tooling and structured interfaces, and it can also be used at runtime.

Conceptual code:

~~~csharp
using UnityEngine;
using UnityEngine.UIElements;

public class ExampleUI : MonoBehaviour
{
    private void OnEnable()
    {
        var root = GetComponent<UIDocument>().rootVisualElement;
        var button = root.Q<Button>("PlayButton");
        button.clicked += OnPlay;
    }

    private void OnPlay()
    {
        Debug.Log("Play");
    }
}
~~~

## UI state management

Do not let every gameplay script directly manipulate every UI widget.

Prefer:

~~~text
Gameplay
  ↓ event/state
UI Controller
  ↓
Widgets
~~~

This makes scene changes and UI replacement easier.

## Responsive layout rules

For different aspect ratios:

- anchor to meaningful edges
- use layout groups
- avoid hard-coded screen pixels for everything
- test ultrawide and tall screens
- consider safe areas on mobile

## Common UI bugs

| Symptom | Likely cause |
|---|---|
| Button not clickable | EventSystem / raycast target / overlay |
| Text clipped | RectTransform / layout |
| UI changes position on aspect ratio | Poor anchors |
| UI is blurry | Canvas scaling / text configuration |
| World-space UI microscopic | Canvas scale mismatch |

## Related

[input.md](input.md) · [content.md](content.md) · [performance.md](performance.md)

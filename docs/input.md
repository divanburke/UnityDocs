# Input System Reference

Unity's newer Input System is package-based and designed around actions, bindings and devices. It is the preferred approach for new projects when the package is available and configured.

## Core concepts

~~~text
Device
  ↓
Control
  ↓
Binding
  ↓
Input Action
  ↓
Action Map
  ↓
Player Input / code
~~~

An action represents intent such as Move, Jump, Interact or Pause rather than a specific physical key.

## Why actions are better

| Old style thinking | Action-based thinking |
|---|---|
| “W key” | “Move forward” |
| “Space key” | “Jump” |
| “Mouse button 0” | “Attack” |
| “Gamepad button” | “Confirm” |

One action can have multiple bindings.

## InputAction basics

~~~csharp
using UnityEngine;
using UnityEngine.InputSystem;

public class PlayerInputReader : MonoBehaviour
{
    [SerializeField] private InputAction move;
    [SerializeField] private InputAction jump;

    private void OnEnable()
    {
        move.Enable();
        jump.Enable();
    }

    private void OnDisable()
    {
        move.Disable();
        jump.Disable();
    }

    private void Update()
    {
        Vector2 movement = move.ReadValue<Vector2>();

        if (jump.WasPressedThisFrame())
        {
            Debug.Log("Jump");
        }
    }
}
~~~

## Important InputAction methods

| API | Meaning |
|---|---|
| Enable() | Start receiving values |
| Disable() | Stop receiving values |
| ReadValue<T>() | Read current typed value |
| ReadValueAsObject() | Read boxed/object value |
| WasPressedThisFrame() | Button/action press occurred this frame |
| WasReleasedThisFrame() | Release occurred this frame |
| IsPressed() | Currently pressed |
| WasPerformedThisFrame() | Action performed this frame |
| Enable/Disable action map | Control a group of actions |

## Callbacks

~~~csharp
private void OnEnable()
{
    jump.performed += OnJump;
}

private void OnDisable()
{
    jump.performed -= OnJump;
}

private void OnJump(InputAction.CallbackContext context)
{
    // Input action fired.
}
~~~

This is useful when event-driven input is clearer than polling.

## PlayerInput component

PlayerInput can connect an Input Action Asset to a player and route action callbacks.

Common behavior choices include:

- Send Messages
- Unity Events
- C# callback-driven patterns

For larger projects, explicit code references are often easier to maintain.

## Input Action Asset

Organize actions into maps:

~~~text
Player
├── Move
├── Look
├── Jump
├── Interact
└── Attack

UI
├── Navigate
├── Submit
├── Cancel
└── Point
~~~

Enable only the maps relevant to the current state.

## Continuous movement

~~~csharp
private Vector2 moveInput;

private void Update()
{
    moveInput = move.ReadValue<Vector2>();
}

private void FixedUpdate()
{
    body.linearVelocity = new Vector2(
        moveInput.x * speed,
        body.linearVelocity.y
    );
}
~~~

## Button edge detection

Use action helpers instead of timing assumptions.

~~~csharp
if (jump.WasPressedThisFrame())
{
    TryJump();
}
~~~

## Dead zones

Stick controls often need dead-zone processing.

An input vector close to zero should not accidentally trigger movement.

Conceptual:

~~~csharp
Vector2 filtered = moveInput;

if (filtered.magnitude < 0.15f)
{
    filtered = Vector2.zero;
}
~~~

## Rebinding

Input System supports runtime rebinding. Store rebinding overrides in player settings so they can persist.

A good UX:

1. Pick action.
2. Enter waiting-for-input state.
3. Capture device control.
4. Validate the binding.
5. Save override.
6. Show the new control.

## Mouse position

The Input System can supply pointer position as a Vector2. Convert it to world space through the Camera when gameplay needs a world point.

## Common input bugs

| Problem | Fix |
|---|---|
| Action does nothing | Action/map not enabled |
| Callback runs multiple times | Duplicate subscription |
| Movement works only sometimes | Wrong action map active |
| Stick drifts | Dead zone |
| Input breaks after scene change | Lifetime/subscription issue |
| Physics movement jitters | Read input in Update, apply body changes in FixedUpdate |

## Legacy input note

Older tutorials use <code>Input.GetAxis</code>, <code>Input.GetButton</code> and <code>Input.GetKey</code>. These can still appear in older projects, but do not mix old and new input assumptions inside one controller without a reason.

## Related

[2d-physics.md](2d-physics.md) · [3d-physics.md](3d-physics.md) · [scripting.md](scripting.md)

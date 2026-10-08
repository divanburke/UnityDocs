# Animation Reference

## Animator

Animator controls the Mecanim animation state machine.

Common responsibilities:

- state transitions
- parameters
- blend trees
- layers
- avatar/rig control
- animation timing
- root motion

## Typical character hierarchy

~~~text
Player
├── Controller / Physics
└── Model
    ├── Animator
    └── SkinnedMeshRenderer
~~~

Keep the visual model separate when gameplay physics should remain stable.

## Animator parameters

| Type | Good for |
|---|---|
| Bool | grounded, alive, aiming |
| Float | speed, direction, blend value |
| Int | mode / weaponless stance / state index |
| Trigger | one-shot events such as jump or attack |

## SetBool

~~~csharp
animator.SetBool("Grounded", grounded);
~~~

Use for persistent true/false state.

## SetFloat

~~~csharp
animator.SetFloat("Speed", velocity.magnitude);
~~~

The damped overload can smooth parameter changes.

## SetInteger

~~~csharp
animator.SetInteger("State", stateIndex);
~~~

Useful where several mutually exclusive modes exist.

## SetTrigger

~~~csharp
animator.SetTrigger("Jump");
~~~

A trigger is designed as a one-shot transition signal.

## ResetTrigger

~~~csharp
animator.ResetTrigger("Attack");
~~~

Useful when a queued transition should be cancelled.

## Animator.Play and CrossFade

~~~csharp
animator.Play("Base Layer.Idle");
~~~

CrossFade allows a smoother transition:

~~~csharp
animator.CrossFade(
    "Base Layer.Run",
    0.1f
);
~~~

Use state-machine transitions for most gameplay-driven animation; direct Play calls are better kept for deliberate overrides.

## StateMachineBehaviour

Use StateMachineBehaviour when logic belongs tightly to an Animator state.

Examples:

- entering a stun state
- notifying an animation system
- enabling a hit window
- applying state-specific effects

Do not hide major gameplay rules inside animation callbacks without documenting them.

## Animation events

Animation events can call functions at a specific point in an AnimationClip.

Good uses:

- footstep sounds
- VFX timing
- cosmetic cues

For critical gameplay, prefer explicit state/timing logic that remains robust if clips are replaced.

## Root motion

Root motion uses animation movement to move a character.

Important question: who owns position?

~~~text
Physics controller → position
OR
Animator root motion → position
~~~

Mixing both without a plan causes fighting and jitter.

## Blend trees

Blend Trees smoothly combine animations according to one or more parameters.

Common examples:

- walk/run by Speed
- strafe by X/Y
- aim direction
- directional movement

## Animation layers

Layers let different animation logic run with separate weights.

Example:

~~~text
Base Layer  → locomotion
Upper Body  → hand/arm animation
Additive    → recoil-like motion / secondary motion
~~~

Use masks to decide which bones a layer affects.

## Character animation state pattern

~~~csharp
private static readonly int SpeedId = Animator.StringToHash("Speed");
private static readonly int GroundedId = Animator.StringToHash("Grounded");
private static readonly int JumpId = Animator.StringToHash("Jump");

private void UpdateAnimator()
{
    animator.SetFloat(SpeedId, currentSpeed);
    animator.SetBool(GroundedId, grounded);
}
~~~

Hashes avoid repeated string lookup and make parameter IDs explicit.

## Common animation bugs

| Bug | Likely cause |
|---|---|
| Animation won't transition | Incorrect parameter/conditions |
| Character slides | Animation speed and movement speed disagree |
| Character teleports | Root motion vs scripted motion |
| Animation only works once | Trigger/state transition setup |
| Upper body affects legs | Missing Avatar Mask |
| Animation changes physics collider | Animation is driving a shared Transform hierarchy |

## Related

[3d-overview.md](3d-overview.md) · [2d-graphics.md](2d-graphics.md) · [scripting.md](scripting.md)

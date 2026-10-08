# Audio Reference

## Core components

| Component | Purpose |
|---|---|
| AudioSource | Plays sound |
| AudioListener | Receives audio, normally attached to camera/player |
| AudioClip | Audio asset |
| AudioMixer | Routes/processes groups |
| AudioMixerGroup | Mixer routing destination |

## AudioSource

Important properties:

| Property | Meaning |
|---|---|
| clip | Default AudioClip |
| outputAudioMixerGroup | Mixer routing |
| volume | Base volume |
| pitch | Playback speed/pitch |
| loop | Repeat |
| playOnAwake | Start automatically |
| spatialBlend | 2D ↔ 3D balance |
| spatialize | Spatial processing where available |
| minDistance / maxDistance | 3D attenuation range |

## Play

~~~csharp
audioSource.Play();
~~~

Play a one-shot sound without changing the source's main clip:

~~~csharp
audioSource.PlayOneShot(clip);
~~~

This is ideal for repeated short SFX.

## Stopping

~~~csharp
audioSource.Stop();
audioSource.Pause();
audioSource.UnPause();
~~~

Pause retains playback position; Stop ends playback.

## 2D sound

Set spatialBlend near 0 for a non-positional UI/menu sound.

## 3D sound

Set spatialBlend toward 1 for positional world audio.

Typical examples:

| Sound | Spatial style |
|---|---|
| Menu click | 2D |
| Explosion in world | 3D |
| Background music | 2D |
| Footstep attached to character | 3D |

## AudioSource pitch

Pitch can be varied slightly for repeated SFX.

~~~csharp
audioSource.pitch = Random.Range(0.95f, 1.05f);
audioSource.PlayOneShot(clip);
audioSource.pitch = 1f;
~~~

Avoid extreme randomization or it will sound artificial.

## Mixer routing

A practical routing layout:

~~~text
Master
├── Music
├── SFX
├── UI
└── Ambience
~~~

This lets the game expose separate volume controls.

## Fading music

Do not instantly switch between tracks if a smooth transition is desired. Two AudioSources can be crossfaded using a coroutine, mixer volume automation, or a dedicated audio manager.

## Audio manager pattern

Keep the manager focused:

~~~csharp
public class AudioManager : MonoBehaviour
{
    [SerializeField] private AudioSource musicSource;
    [SerializeField] private AudioSource sfxSource;

    public void PlaySfx(AudioClip clip)
    {
        if (clip != null)
        {
            sfxSource.PlayOneShot(clip);
        }
    }
}
~~~

## Avoid the one-source trap

One AudioSource cannot conveniently represent every simultaneous sound. For repeated effects, PlayOneShot or a pool/multiple sources is more appropriate.

## Common audio bugs

| Symptom | Likely cause |
|---|---|
| Nothing audible | No AudioListener / muted output |
| Sound too quiet | Mixer / volume / attenuation |
| 3D sound seems 2D | spatialBlend / distance settings |
| Repeated clips cut each other off | Using Play instead of PlayOneShot |
| Music duplicates after scene load | Persistent manager duplication |

## Related

[content.md](content.md) · [performance.md](performance.md) · [debugging.md](debugging.md)

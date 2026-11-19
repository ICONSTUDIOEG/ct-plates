# Cinematic Tutor · Plate 01 (The microphone) · Integration guide

For: the Cinematic Tutor app team. Plate version 1.1.0.

## What you get

| File | What it is |
|---|---|
| `index.html` | The whole plate in one file (about 1.2 MB): 3D, audio engine, fonts. No other files and no internet needed. |
| `host-demo.html` | A reference host. It embeds the plate, drives it the way Mr. Aly would, and logs every message. Open it next to this guide. |

## 1 · Put it on the institute site

Upload `ct-plate01.html` anywhere on the site, served over **HTTPS**. Browsers allow the microphone only on HTTPS (or `localhost`, or a file opened from disk).

To give a plate its own page, link to the file directly.

To embed it in a lesson page:

```html
<iframe
  src="/plates/ct-plate01.html?host=https://cinematictutor.example"
  allow="microphone; autoplay"
  style="width:100%; aspect-ratio:16/10; border:0"
  title="Plate 01 · The microphone"></iframe>
```

- `allow="microphone"` is required. Without it, "Try your voice" is refused and the plate falls back to the synthesised voice, saying so on screen.
- The plate lays out as a full sheet from about 900 px wide, and as a vertical scroll below that.
- Sound starts only after the student clicks inside the plate. This is a browser rule, and a click in the host page does not count.

## 2 · URL parameters

| Param | Values | Effect |
|---|---|---|
| `mic` | `measure` `dynamic` `ldc` `pencil` `ribbon` `shotgun` | Microphone to open with. The default is `ldc`. |
| `view` | `studio` `cutaway` `field` `exploded` | View to open in. |
| `narration` | `host` | Hides the plate's narration bar, so Mr. Aly speaks instead. Lines are still sent to the host. |
| `tasks` | `off` | Hides the lesson-task panel, if the app shows progress itself. |
| `host` | an origin, e.g. `https://app.example` | Pins the origin the plate sends to and accepts commands from. Use this in production. |

## 3 · The bridge (postMessage)

Everything the plate sends has `source: 'ct-plate'`. Everything it accepts has `target: 'ct-plate'`.

### Plate → host

```js
{ source:'ct-plate', plate:'plate01', version:'1.1.0', type:'ready', manifest:{ title, mics, views, tones, detents, objectives, lines } }
{ ..., type:'narration', id:'proximity', ar:'…', en:'…' }    // every line the plate says: give it to Mr. Aly
{ ..., type:'progress', done:['null','proximity'], total:6, complete:false, last:'proximity' }
{ ..., type:'event', name:'mic' | 'null-reached' | 'proximity-entered' | 'proximity-left' | 'clip', data:{ id } }
{ ..., type:'result', id:7, ok:true, value:… }                // or ok:false, error:'…'
```

`ready` arrives once the plate has loaded. Wait for it before sending commands.

### Host → plate

```js
frame.contentWindow.postMessage({ target:'ct-plate', id: 7, action:'selectMic', args:['shotgun'] }, plateOrigin);
```

`id` is optional. When it is given, the plate replies with `type:'result'` and the same id.

| action | args | does |
|---|---|---|
| `selectMic` | `id` | Switches the microphone (body, pattern, sound). |
| `setPattern` | `detentId` or `k` (−1…1) | Turns the knob. Only on `ldc`; other mics have fixed patterns and return an error. |
| `setView` | `id` | Changes the view, with its camera move. |
| `placeSource` | `id, r_metres, angle_deg` | Moves `voice`, `guitar` or `noise`. The angle is measured from the mic's front. |
| `rotateMic` | `deg` | Turns the mic on the stage. |
| `setGain` | `dB` (0…36) | Sets the preamp gain. |
| `setTone` | `0` `100` `1000` `8000` | Test tone from the voice position. |
| `mute` / `solo` | `id, on` | Source buttons. |
| `say` | `lineId` or `{ar, en}` | Shows a narration line now. It is echoed back as `narration`. |
| `narration` | `'host'` or `'plate'` | Hides or shows the plate's own narration bar. |
| `sound` | `true` or `false` | Starts or stops audio. Starting may fail until the student has clicked in the plate. |
| `slow` | `true` or `false` | Slow motion. |
| `getState` | none | Returns `{ mic, k, view, yawDeg, gainDb, toneHz, sound, vu, sources[], readings, progress }`. |
| `resetProgress` | none | Clears the student's ticked tasks. |

## 4 · Mr. Aly mode

1. Embed with `?narration=host`.
2. On every `narration` message, have Mr. Aly speak `ar`, using your TTS or his recorded voice. Show `en` as a subtitle if you like.
3. Drive the lesson with commands. `host-demo.html` has the full nine-step script (`STEPS`). Copy it, then retime it to Mr. Aly's real speech durations instead of the fixed waits.

All narration text lives in `manifest.lines`, keyed by id. That is the voice-over script, ready to record once.

## 5 · Lesson tasks and progress

The plate ticks six tasks as the student actually does them: try all six mics, put the noise in a null, use proximity, compare omni, double the distance, turn the shotgun at 8 kHz. Progress is kept in the student's browser (`localStorage`, key `ct-plate01-progress`) and sent to you as `progress`. To record it per student, store it on your side against your own login. The plate never sees or sends personal data.

## 6 · Notes

- This guide's numbers are true of this build. Physics values are tested. Product-like details (voicing, tube length, null depth) are modelled and labelled «تقديري» on the sheet.
- Rebuild from source: `node tools/bundle.mjs <esbuild-dir>` produces `dist/ct-plate01.html`. `npm run check` runs the 56-assertion physics check.

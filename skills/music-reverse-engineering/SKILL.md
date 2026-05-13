---
name: Music Reverse Engineering
description: 'Use when the user needs: Reverse engineer and rebuild music from a URL or local audio into a production-ready breakdown, with arrangement, tonal, artifact, and rebuild guidance that stays copyright-safe and original.'
icon: icon.svg
metadata:
  depends_on: [crystallize]
  category: content/media
  family: design
  lifecycle: active
  canonical_slug: music-reverse-engineering
  icon_style: craft-category-glyph-v1
---

# Music Reverse Engineering

Reverse engineer and rebuild a track from a URL or local audio into a
production-ready breakdown. Use when the user asks to decompose music, analyze
arrangement, estimate tempo/key, or rebuild a similar-but-original version from
a song or YouTube link.

## When to Use

- The user shares a YouTube or audio URL and wants the music broken down
- The task is to identify tempo, key center, structure, instrumentation, and
  mix character
- The goal is to rebuild the vibe and production method without cloning
  copyrighted melody or lyrics
- The user wants a DAW-ready recipe, section map, or original scaffold from an
  existing reference track

## Workflow

1. Resolve the source.
   - Pull title, channel, duration, and source URL with `yt-dlp` or equivalent.
   - For local audio, record the path plus `ffprobe` metadata before analysis.
   - Download `bestaudio` to a temp folder unless the operator explicitly
     wants the source preserved.
2. Create analysis assets.
   - Use `ffprobe` for codec, sample rate, channel count, and duration.
   - Transcode a short mono analysis file with `ffmpeg` at low sample rate.
   - Default to the first 5 minutes for ambient or loop-based material; for
     evolving tracks, clip representative sections instead of the whole file.
   - If running on JARVIS with `/Volumes/☯Duality` mounted and the result
     should persist, write the human report under
     `./ShadowArchive/80-reports/music-reverse-engineering/`. Otherwise keep
     derived audio in temp space.
3. Extract musical anchors.
   - Estimate pulse with lightweight onset or envelope autocorrelation.
   - Estimate pitch center with FFT-bin-to-pitch-class mapping if `librosa` is
     unavailable.
   - Measure section energy in 30-second windows for arrangement clues.
   - Capture spectral centroid and rolloff for brightness/air/texture.
4. Turn measurements into production language.
   - Convert BPM candidates into a practical session tempo.
   - Convert pitch-class concentration into a likely key or tonal center.
   - Interpret energy windows as intro, sustain, lift, dip, and outro zones.
   - Identify likely roles: drone, lead flute, pad, bells, percussion, FX bed.
   - Report confidence and assumptions explicitly when pulse, key, or section
     boundaries are weak.
5. Rebuild as an original.
   - Give a DAW recipe: tempo, scale, bar-count loop, sound sources, effects,
     arrangement order, and mix chain.
   - Avoid exact melodic transcription for copyrighted material. Recreate
     structure, timbre, harmonic gravity, and emotional contour instead.
   - If the user asks for MIDI or chords, provide an original approximation,
     not the source melody line.
6. Package the answer with the output contract below.
7. If the operator says `crystallize`, invoke the dedicated `crystallize`
   skill and prefer updating this skill over creating a parallel duplicate.

## Key Decisions

- Source persistence:
  Default to temp downloads and temp derived audio. Keep durable artifacts only
  when the operator asks or when a report should live under `ShadowArchive`.
- Analysis window:
  Use the first 5 minutes for ambient, meditation, or loop-based videos. Use
  representative clips if the track clearly evolves.
- Tempo confidence:
  If candidates cluster and percussion is weak, report an "effective grid"
  rather than a hard beat.
- Tonal language:
  If drones or modal ambiguity dominate, phrase the result as tonal center or
  pitch pool instead of forcing a major/minor key.
- Copyright boundary:
  Decompose style, harmony, texture, and structure. Do not provide
  note-for-note melody, solo, or lyric reconstruction.

## Gotchas

- `librosa` is often missing on clean hosts. Fall back to `ffmpeg` +
  `numpy/scipy`.
- Long ambient files can waste time. Trim analysis clips early.
- Pulse detection on rubato flute or drone music will overfit. Treat BPM as
  approximate.
- Strong drones can bias key detection. Phrase the result as tonal center when
  needed.
- Video titles often overstate instrumentation or mood. Trust the audio, not
  the thumbnail/title copy.
- Stereo FX can mask the real lead source. Distinguish source role from
  post-processing.
- Do not leave copyrighted downloads sitting in long-lived staging folders
  unless the operator explicitly wants them preserved.

## Output Contract

- **Source summary**
  - title / creator / duration / source or local path
  - analysis window used
  - toolchain or fallback path used if relevant
- **Reverse-engineered breakdown**
  - effective tempo or tempo range
  - key / tonal center / pitch pool
  - section map with approximate durations
  - instrumentation and role guesses
  - mix / FX character
  - confidence notes where ambiguity exists
- **Rebuild recipe**
  - DAW tempo and meter
  - original harmonic or modal starting point
  - sound sources / patch ideas
  - arrangement order and variation plan
  - core mix chain
- **Copyright-safe boundary**
  - one short note on what was intentionally not copied
- **Optional scaffold**
  - MIDI or chord starter only as an original approximation, never
    source-faithful transcription

## Crystallized From

- Session: 2026-04-17 on JARVIS
- Original task: Reverse engineer and decompose/rebuild the music from a
  YouTube meditation-flute video
- Pattern extracted: bounded audio analysis -> production-language
  interpretation -> explicit original rebuild contract

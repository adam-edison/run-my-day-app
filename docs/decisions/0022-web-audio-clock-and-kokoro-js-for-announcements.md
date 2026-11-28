# DR-0022: Web Audio Clock and kokoro-js for Announcements in the Web Build

**Status:** Proposed  
**Date:** 2026-11-27

## Context

Requirement: the first sound of each step plays within 100 milliseconds of the moment the step is due to start, with no drift over a run.

Announcements are spoken text built from templates at run time, such as "Squats, 1 minute." Run My Day works fully offline.

Chrome checks the timers of a hidden tab once per second.

The Web Audio clock schedules playback to the sample. An AudioContext created before a user gesture starts in the suspended state.

## Decision

Timing:

* Every sound is scheduled ahead on the Web Audio clock with `AudioBufferSourceNode.start(when)`.
* A timer loop schedules each sound that falls inside a lookahead window. Sounds are never started directly by a timer.
* The lookahead is long enough to cover hidden-tab timer throttling.
* The AudioContext is started by a user gesture, such as pressing Start.

Speech:

* kokoro-js generates each announcement as audio in the browser, before the announcement is due.
* The first trial uses the q8f16 model. A larger model is used only if the voice quality is not good enough.
* The model is downloaded once and kept for offline use.

The status stays Proposed until two trials pass in the Expo web build:

* Scheduled sounds play within 100 milliseconds of their start mark in a hidden tab.
* kokoro-js generates an announcement of 200 characters before it is due.

Example: a posture held for 4 seconds is looped. A few seconds before each loop, the step start announcement is generated and scheduled on the Web Audio clock. Each loop's first sound plays on its 4-second mark, with the tab hidden.

## Alternatives Considered

The Web Speech API (`speechSynthesis`), rejected:

* It speaks an utterance when it reaches the front of a queue, and cannot be scheduled at an exact time.
* Its output cannot be captured as audio.
* Only local voices work offline.

Starting sounds directly with `setTimeout`, rejected:

* A hidden tab's timers are checked once per second.

## Rationale

* The Web Audio clock schedules playback to the sample.
* A hidden tab's timers run once per second. A lookahead window longer than that keeps each sound scheduled before it is due.
* kokoro-js output is audio that the Web Audio clock can schedule.
* Speech is generated on the device, so it works offline.

## Consequences

* The q8f16 model is an 86 MB download. A larger model would be a larger download.

## Assumptions

* The Web Audio clock stays on time in a hidden tab. Not found in official sources as of 2026-11-28. A trial checks this.
* kokoro-js generates an announcement fast enough to finish before it is due. Not found in official sources as of 2026-11-28. A trial checks this.

## Sources

### [AudioBufferSourceNode: start() method - MDN](https://developer.mozilla.org/en-US/docs/Web/API/AudioBufferSourceNode/start)

Retrieved 2026-11-27.

- Fact: `start(when)` schedules playback on the AudioContext clock.
  > "The time, in seconds, at which the sound should begin to play, in the same time coordinate system used by the AudioContext."

### [Web Audio API specification](https://webaudio.github.io/web-audio-api/)

Retrieved 2026-11-27.

- Fact: The Web Audio clock schedules playback to the sample.
  > "Sample-accurate scheduled sound playback"

### [A tale of two clocks | web.dev](https://web.dev/articles/audio-scheduling)

Retrieved 2026-11-27.

- Fact: A timer loop schedules sounds ahead on the Web Audio clock.
  > "The Web Audio API exposes access to the audio subsystem's hardware clock."
  > "A good place to start is probably 100ms of "lookahead" time, with intervals set to 25ms."

### [Heavy throttling of chained JS timers beginning in Chrome 88](https://developer.chrome.com/blog/timer-throttling-in-chrome-88)

Retrieved 2026-11-27.

- Fact: Chrome checks the timers of a hidden tab once per second.
  > "The browser will check timers in this group once per second."

### [Autoplay policy in Chrome](https://developer.chrome.com/blog/autoplay)

Retrieved 2026-11-27.

- Fact: The AudioContext is started by a user gesture.
  > "If an AudioContext is created before the document receives a user gesture, it will be created in the "suspended" state, and you will need to call resume() after the user gesture."

### [Web Speech API specification](https://webaudio.github.io/web-speech-api/)

Retrieved 2026-11-27.

- Fact: The Web Speech API adds an utterance to a queue and cannot schedule it at an exact time.
  > "This method appends the SpeechSynthesisUtterance object utterance to the end of the queue"
- Fact: Only local voices work offline.
  > "This attribute is true for voices supplied by a local speech synthesizer, and is false for voices supplied by a remote speech synthesizer service."

### [kokoro.js README](https://github.com/hexgrad/kokoro/blob/main/kokoro.js/README.md)

Retrieved 2026-11-27.

- Fact: kokoro-js generates speech as audio in the browser.
  > "Kokoro is a frontier TTS model for its size of 82 million parameters (text in/audio out)."

### [onnx-community/Kokoro-82M-v1.0-ONNX files](https://huggingface.co/onnx-community/Kokoro-82M-v1.0-ONNX/tree/main/onnx)

Retrieved 2026-11-27.

- Fact: The q8f16 model is an 86 MB download.
  > "model_q8f16.onnx" "86 MB"

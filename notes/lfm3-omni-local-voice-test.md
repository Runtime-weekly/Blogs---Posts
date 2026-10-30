# LFM3 Omni: a local vision-and-voice test

Published October 30, 2026 · RUNTIME

[Watch the RUNTIME episode: LFM3 Omni — A Small AI That Sees, Hears and Talks Offline](https://youtu.be/uoVL3HP-Ky8)

Can a small model look at a picture, hear a question and speak a useful answer without a cloud connection? We tested LiquidAI's **LFM3-Omni-2B** with recorded questions, a simple shelf illustration and one photograph. The video plays seven complete questions and the model's original spoken replies, keeping the measured waits between them.

The model answered the spoken shelf questions correctly in these runs, rejected a nonexistent yellow box and followed a mid-sentence correction. It also produced a contradictory typed answer, and its transcription mode invented a word from silence. This is a small capability check, not an accuracy benchmark or a finished voice assistant.

## What was tested

- Model: [LiquidAI/LFM3-Omni-2B](https://huggingface.co/LiquidAI/LFM3-Omni-2B), exact revision [`5797d07c125503bd4f4c0e7f915ae2d3d703e8de`](https://huggingface.co/LiquidAI/LFM3-Omni-2B/tree/5797d07c125503bd4f4c0e7f915ae2d3d703e8de).
- Hardware: NVIDIA GB10 in a shared DGX Spark. This does not establish laptop or phone performance.
- Runtime: Python 3.12.3, Transformers 5.21.0, PyTorch 2.15.0+cu132, BF16 weights, with the reference convolution fallback rather than an optimized implementation.
- Two fresh processes used the same fixed inputs and settings. IPv4/IPv6 socket creation was blocked for the inference process and its children; a connection attempt was verified to fail. Model files were already downloaded.
- Seven cases requested interleaved text and speech from LFM3 itself. These use `max_new_tokens=1024`, `do_sample=False`, `audio_temperature=0.4` and `audio_repetition_penalty=1.2`. No spoken reply reached the output budget. Text-only controls use a 120-token budget; the recorded seed was 71.
- Questions were fixed synthetic speech recordings. The noisy case adds fixed artificial background noise. These were not live microphone conversations. Raw voice recordings are not redistributed here.

The [pinned model card](https://huggingface.co/LiquidAI/LFM3-Omni-2B/blob/5797d07c125503bd4f4c0e7f915ae2d3d703e8de/README.md) describes separate vision/audio encoders feeding the LFM3 backbone, with audio codes decoded into speech. This explains how a single model package combines the inputs; it is not a claim that every component is identical. Video input and other LFM3 variants were not tested.

## Inputs and results

The shelf illustration has three shelves. The top shelf contains a blue box and a green box, the middle shelf a blue box, and the bottom shelf a red box. There is no yellow box.

| Test | Question or task | Result in both runs |
| --- | --- | --- |
| Typed count | How many blue boxes are on the shelves, and which shelf are they on? | Counts two, but says both are on the top shelf and then describes a blue box on the middle shelf. Internally contradictory. |
| Spoken count | The same question, with audio input and spoken output | Says two, on the top and middle shelves. |
| Red-box reply | What color is the box on the bottom shelf? Answer in one short sentence. | Red. |
| Noisy spoken count | The counting recording with artificial noise added | Answer text says two, on the top and middle shelves. See the pronunciation note below. |
| False premise | What color is the yellow box on the middle shelf? | Rejects the yellow-box premise and identifies the middle box as blue. |
| Instruction correction | Put the red box on the top shelf. Actually, leave the red box where it is and move the blue box instead. Which box should move? | Selects the blue box. This is an audio-only instruction; it does not move a real object. |
| Clean/noisy transcription | Transcribe the corresponding counting recordings | Recovers the question's words. |
| Silence transcription | Three seconds of silent audio | Returns `Okay.` despite no spoken word. This is a text-only result. |
| Photograph description | Describe what the person is doing and name two visible objects. Keep your answer to two short sentences. | Mentions the camera, clipboard and pen; the phrase about sitting in a spacecraft control panel is awkward. |
| Photograph detail | Is the person holding a phone or a pen? What evidence can you see? | Chooses a pen and refers to the hand and clipboard. |

The photograph is NASA's [Eileen Collins, Discovery Pilot](https://www.nasa.gov/image-article/eileen-collins-discovery-pilot/), showing Collins aboard Discovery in 1995. Credit: NASA. The test asks about visible activity and objects, not the person's identity.

The spoken replies are original model audio, not a narrator reading the typed baseline. The typed and speech-output cases use different inputs and response settings; these examples do **not** prove that speaking improves reasoning. All answer text matched across the two fresh runs, including the errors. Matching text does not establish byte-identical speech or general accuracy.

Pronunciation note: two automatic transcription passes heard “glue” where the saved noisy-count answer text says “blue.” The original model audio is retained in the video. This is a possible pronunciation ambiguity, not a separately verified listening judgment or a reason to silently replace the model's speech.

## Response times

The timer starts with the complete saved question available, before request construction and input preparation. It ends after full generation, text/audio decoding, saving the output and GPU synchronization. Test-program overhead is included, with nothing subtracted.

These are **complete-response times**, not first-token or first-audio latency. Input recording duration, model loading, remote command dispatch, editing pauses and physical playback are separate. Microphone capture, end-of-speech detection, speaker buffering, streaming and interruption behavior were not measured.

| Case | Output | Run A (s) | Run B (s) | Run A spoken playback (s) |
| --- | --- | ---: | ---: | ---: |
| Warm-up (separate) | text only | 1.765 | 1.462 | — |
| Typed count | text only | 1.961 | 1.411 | — |
| Spoken count | speech + text | 3.691 | 3.235 | 5.92 |
| Red-box reply | speech + text | 1.614 | 1.545 | 2.88 |
| Noisy spoken count | speech + text | 3.228 | 3.113 | 6.32 |
| False premise | speech + text | 2.958 | 2.924 | 5.92 |
| Instruction correction | speech + text | 1.167 | 1.212 | 2.40 |
| Clean transcription | text only | 0.442 | 0.443 | — |
| Noisy transcription | text only | 0.430 | 0.424 | — |
| Silence transcription | text only | 0.109 | 0.102 | — |
| Photograph description | speech + text | 5.503 | 5.507 | 11.28 |
| Phone or pen? | speech + text | 4.462 | 4.396 | 8.80 |

The opening red-box reply took 1.614 seconds in run A: approximately 1.497 seconds generating, plus 0.118 seconds for preparation, decoding and output handling. Independently rounded components can differ from the rounded total. The completed answer then takes 2.88 seconds to play. Waiting and listening are different parts of the experience.

Model and processor/codec loading took **26.83 seconds** in run A and **21.15 seconds** in run B. Program launch and the warm-up question are additional. The warm-up is listed separately rather than counted as a steady-state performance sample.

Peak PyTorch-allocated GPU memory was about **4.59 GB / 4.28 GiB** across these tests. This excludes other device/process usage and is not a minimum system-RAM requirement. Two runs on shared hardware do not establish a latency distribution, speed ranking or guarantee.

## Try a comparable check

Use the pinned model card's [Transformers quick start and usage examples](https://huggingface.co/LiquidAI/LFM3-Omni-2B/blob/5797d07c125503bd4f4c0e7f915ae2d3d703e8de/README.md#transformers-quick-start), including the interleaved voice-chat and speech-recognition examples. Review the model's license and custom loading code before use. This note links the authors' setup; it is not a newly tested installer or a distribution of our runtime environment.

1. Create a small diagram whose answer you can check manually, such as the shelf arrangement above. Save the same image for every run.
2. Record a question once. Keep that input unchanged when comparing settings or processes. Save text and original generated speech separately.
3. Compare a typed question, a spoken question and a separate transcription request. Do not assume successful speech recognition means the answer is correct.
4. Include a false premise, a correction and silent audio. Clearly distinguish transcription-only tasks from spoken replies.
5. Record loading, complete-response generation and playback duration separately. To compare against this table, include preparation and decoding in the response timer.
6. Repeat in a fresh process and keep failures. If you build a live voice application, separately measure its entire microphone-to-speaker loop with different voices and acoustic conditions.

A practical next experiment is an occasional local helper for screenshots, diagrams or photos. These results support exploring that idea; they do not establish that a reliable always-listening desktop application already exists.

## Sources and scope

- [Official model and license](https://huggingface.co/LiquidAI/LFM3-Omni-2B)
- [Exact tested snapshot](https://huggingface.co/LiquidAI/LFM3-Omni-2B/tree/5797d07c125503bd4f4c0e7f915ae2d3d703e8de)
- [Pinned architecture, generation settings and usage examples](https://huggingface.co/LiquidAI/LFM3-Omni-2B/blob/5797d07c125503bd4f4c0e7f915ae2d3d703e8de/README.md)
- [NASA photograph and credit](https://www.nasa.gov/image-article/eileen-collins-discovery-pilot/)

The results above come from RUNTIME's saved local experiment. The episode's illustrations explain the method; they are not a live screen recording. Playback levels were normalized while the model questions, replies and measured waits were preserved. Broader model capability claims belong to the upstream sources and require separate evaluation.

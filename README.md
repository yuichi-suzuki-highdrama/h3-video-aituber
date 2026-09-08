# Build an Animated AITuber with H3 — From Character Art to Motion, Lip Sync and Conversation

This repository shares procedural notes only. It does not distribute source code, executable configuration, generation workflows, character assets, or model weights. Obtain tools from their official sources under the terms applicable to you.

English | [日本語](README.ja.md)

[Try the interactive demo — Fumikiri Wataru](https://wataru.highdrama.net/) · Prerecorded voice samples and motion, without a live AI connection.

Fumikiri Wataru began with a question: could the fluid hair and body motion in generated video be reused during a stream? This experiment separates offline generation from runtime playback. H3 supplies prerecorded motion; a browser connects clips and composites an audio-driven mouth. Images, videos and speech are AI-generated.

This is neither a 3D rig nor a Live2D model. These notes share the method, not character assets or model weights. The local AI version and the prerecorded public demo are different applications.

## 1. Establish one canonical image

The initial design came from niji・journey. GPT Images was used to adjust framing and produce multiple views. Fix the camera, character scale and canvas before animating. Our portrait canvas is 768×1344; the normal pose includes the tall ears and both hands.

Generated green backgrounds are not guaranteed to be a constant RGB color. A simple chroma key can also remove green eyes or costume details. Split body/head reference sheets into separate images before R2V. A roughly 1024px short-edge cap before max reference sizing was a starting point in earlier experiments, not a universal optimum.

## 2. Start with two poses

Use front and shallow-left before building the full set. Our final set has nine poses: front, three approximate yaw angles on each side, looking up and looking down. Labels 15/30/60 describe requested angles, not calibrated measurements. Edit head/hair/ears while retaining torso, shoulders, hands, costume and camera. Generated edits still require registration and visual inspection.

## 3. Generate first/last-frame transitions

Generate each direction separately. Front ↔ eight other poses requires 16 clips. Our local FL2VA INT8 + task-matched PDD8 setup produced 768×1344, 24fps, 73-frame clips (~3.04s). This short local setting is not a promise about official API duration support. Do not mix Ref2VA and FL2VA adapters.

Use the corresponding pose images as endpoints, a fixed camera, a closed mouth and consistent green background. We requested motion from 0.4s to 2.5s, then checked the actual result. Follow the official prompt guide's visual/soundscape/music structure. Reuse the canonical front endpoint for gestures.

A small horizontal scale jump was visible when starting clips. A centered 1.0155× horizontal correction helped this character. Measure your own assets; do not copy this factor blindly. Mouth tracking and background correction must operate in the same transformed coordinate system.

## 4. Source mouths from H3 itself

An earlier working pipeline pasted six Qwen-Image-Edit mouth sprites onto closed-mouth H3 loops. The drawing style, color and surrounding skin did not match reliably. Repeated edits fixed one issue and introduced another.

The current approach extracts mouth crops from H3 speech clips. Natural sentences were more useful than isolated vowels, which tended to produce exaggerated openings. Shallow-left received a dedicated speech clip and new crops when its mouth looked wrong. A wide “i” shape also looked like a smile and needed correction.

We did not validate H3's Japanese visemes. Lip rounding and lateral spreading were inconsistent; some vowel-labelled sprites are approximations. Same-model crops reduce a style mismatch, but do not establish phonetic correctness.

## 5. Track, erase, composite

Initialize mouth position, scale and rotation using SIFT correspondences and a RANSAC partial affine transform. Track subsequent frames with Lucas–Kanade optical flow, plus small corrections from dark lines near the mouth. Generic face detection was unreliable on this anime character.

Store per-frame center x/y, width, height, roll, scale, visibility and approximate yaw. Erase the original closed line with a small mask and OpenCV inpainting. Preserve the original closed mouth in an alpha atlas. Canvas restores that patch during silence and draws the speech mouth when active.


Set visibility to zero for back views and off-screen faces. Inspect tracking and mask edges across the entire clip. These settings are character-specific, not a general automatic anime-face tracker.

## 6. Drive the mouth from audio

For an early known sample, Whisper large-v3 supplied word timestamps. Mora/vowel events were approximated by dividing word intervals; this was not phoneme forced alignment.

Current arbitrary conversation audio and demo samples use amplitude-driven opening. Compute RMS in approximately 60 windows per second, map energy to opening and apply short smoothing. Too little smoothing looks like flapping; too much looks sluggish. A user-preferred 6-frame extension at 24fps stretches the mouth animation by about 0.25s. It does not slow the audio or simply freeze the final mouth pose.

## 7. Add gestures and idle behavior

The nine gestures are wave, body turn, sway, coy pose, hand heart, handstand, jump, bow and high kick. The body turn is a physical turn in front of a fixed camera, not an orbiting camera; hair and clothing should lag with inertia.

Four idle clips adjust hair, glance sideways, join hands and shift weight. Each is 158 frames at 24fps (~6.58s), 768×1344, FL2VA PDD8, using the canonical front image at both ends. Check endpoints rather than assuming a perfect loop.

The scheduler holds front for 0.7–1.7s and shallow side poses for 2.5–4.5s. It lets each gesture finish. Gesture joins retain the final frame instead of fading between unrelated poses; angle transitions retain a brief blending treatment. Side-to-side movement still routes through front. Jump background artifacts needed repair. Handstands and high kicks intentionally allow cropping in the fixed close framing.

## 8. Connect local speech and conversation

Irodori-TTS-v4.1-Small created a synthetic husky feminine voice. Voice Design starts without reference audio; the generated voice then becomes the reference for later utterances. A pitch-shift experiment sounded mechanical and was rejected. The selected voice was generated anew without pitch processing. Japanese phonetic spelling helps with the character name.

Load the model and codec once into a persistent runtime and reuse it for serial synthesis requests. Keep this server local. This is application-level persistence, not an OS startup service.

We integrated @aituber-onair/chat 0.54.0, not the complete OnAir application or its character assets. Initial conversation used GLM-5.3-Flash-EXL3 on a separate machine. The current version uses LM Studio / openai/gpt-oss-20b on the RTX 5090.


Allowlist gestures and keep code execution unavailable to the LLM. In our LM Studio setup, text-only JSON instructions triggered a peg-native error; an explicit response_format JSON Schema resolved it. This is an observed compatibility workaround, not a universal requirement.

When asked about skills, a reply naming high kick or handstand schedules the matching move after speech. Respect disabled automatic/large-motion modes. Serialize playback so gestures do not overlap.

A short greeting measured ~0.39s for the LLM and ~1.13s for TTS, ~1.55s for the whole API request. A repeated short TTS test took 1.70s initially and 0.95s on the next resident call. These are a few local measurements affected by text, cache, voice settings and GPU load, not guaranteed streaming latency.

## Reproduce incrementally

1. Prepare your own canonical front and shallow-side images.
2. Generate two FL2VA transitions and inspect scale/position jumps.
3. Generate natural speech at both poses and extract suitable mouth crops.
4. Validate tracking, erasure and compositing on a short sequence.
5. Add amplitude-driven playback using audio you can lawfully use.
6. Expand directions and gestures only after that works; connect the LLM last.

Build a working ComfyUI GUI workflow first and export its API graph. Node names and parameters depend on installed versions. Before submission, verify reference order, endpoints, dimensions, frame count and task-matched adapters. This repository contains procedural documentation only; implement these steps with your own assets and appropriately licensed tools.

## Public demo boundary

The demo offers prerecorded introduction/high-kick/handstand samples. It has no model, API key, local IP, conversation history, generation server, arbitrary text field or file upload. No download feature is provided, but delivered media cannot be made technically impossible to retrieve or record.

Hosting, including Sites, does not replace content permissions. H3 output publication proceeds under terms the creator has confirmed; these notes do not claim that every hobbyist or jurisdiction is exempt. Readers must check the terms applicable to their models and services.

## Limitations

No general vowel recognition for new speech, jaw/cheek articulation, independent facial expressions or blinks, or direct side-to-side transitions. Blinks inside video clips are baked in. Costume and hand details can drift. The result depends on selection and repair, not one-shot generation.

[Credits and sources](CREDITS.md)

Related generation-speed experiments: https://github.com/yuichi-suzuki-highdrama/minimax-h3-local-pipeline-notes

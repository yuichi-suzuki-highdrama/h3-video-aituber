# Credits / クレジット

AI-generated images, video and synthetic speech / 画像・映像・音声に生成AIを使用しています。

| Component / 工程 | Credit / 提供元 | Role / 用途 |
|---|---|---|
| Character design | niji・journey — Midjourney / Spellbrush | Initial character design / 原案画像 |
| Image editing | GPT Images — OpenAI | Framing and multi-view keyframes / 構図・多方向画像 |
| Video generation | MiniMax H3 — MiniMax | Offline FL2VA/Ref2VA motion / 事前生成の動作 |
| Acceleration | alibaba-pai MiniMax-H3-Acc-LoRAs; ComfyUI-MiniMax-H3-PDD-Acc | Task-matched PDD8 / 対応タスクの8-step生成 |
| Generation UI | ComfyUI — Comfy Org and contributors | Local workflow execution / ローカル生成 |
| Synthetic voice | Irodori-TTS-v4.1-Small — Aratako / Chihiro Arata | Voice design and Japanese TTS / 合成声・日本語音声 |
| Local conversation | gpt-oss-20b — OpenAI; LM Studio — Element Labs | Local version only / ローカル会話版のみ |
| Chat integration | AITuber OnAir — Yuki Shindo / shinshin86 | @aituber-onair/chat 0.54.0; local version only |
| Audio timing | Whisper large-v3 — OpenAI | Earlier known-sample word timestamps / 既知音声の単語時刻 |
| Earlier mouth experiment | Qwen-Image-Edit — Qwen team; MotionPNGTuber | Earlier prototype; not the current mouth-source method |
| Tracking/compositing | OpenCV contributors; NumPy; Pillow; FFmpeg | Offline processing / 前処理 |
| Implementation assistance | OpenAI Codex | Iterative implementation / 実装補助 |

Public demo: prerecorded H3 video + Irodori speech. No LLM, TTS inference, or OnAir package runs on the public site. No model weights are distributed. / 体験版は事前生成素材の再生のみです。モデル重みは配布しません。

## Sources and terms / 参照先と条件

- MiniMax H3: https://github.com/MiniMax-AI/MiniMax-H3
- H3 Community License: https://huggingface.co/MiniMaxAI/MiniMax-H3/blob/main/LICENSE
- PDD: https://huggingface.co/alibaba-pai/MiniMax-H3-Acc-LoRAs
- PDD nodes: https://github.com/Jalen-Brunson/ComfyUI-MiniMax-H3-PDD-Acc
- ComfyUI: https://github.com/Comfy-Org/ComfyUI
- Irodori code (MIT): https://github.com/Aratako/Irodori-TTS
- Irodori model (MIT + model-card ethical restrictions): https://huggingface.co/Aratako/Irodori-TTS-v4.1-Small
- Irodori dependencies acknowledged by its author: Echo-TTS, DACVAE, modernbert-ja-310m and Sony SilentCipher. Existing audio watermarking is retained.
- gpt-oss: https://huggingface.co/openai/gpt-oss-20b
- LM Studio: https://lmstudio.ai/
- AITuber OnAir (MIT): https://github.com/shinshin86/aituber-onair
- Whisper: https://github.com/openai/whisper
- Qwen-Image-Edit: https://github.com/QwenLM/Qwen-Image
- niji・journey: https://nijijourney.com/
- OpenAI service terms: https://openai.com/policies/terms-of-use/

MiniMax H3 is licensed under the MiniMax H3 Community License Agreement, Copyright © 2026 MiniMax. All Rights Reserved.

Third-party licenses remain separate. Attribution does not grant blanket permission to redistribute models, service outputs or character assets. The author's confirmed publishing arrangements do not establish an exemption for other users. / 各モデル・サービスの条件は別々です。クレジットの記載だけで、素材やモデルを自由に再配布できるようになるわけではありません。


Earlier comparison: GLM-5.3-Flash-EXL3 on GX10. No checkpoint or output from that comparison is distributed here.

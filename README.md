# MOSS-Image Workflows

ComfyUI workflows for [MOSS-Image](https://github.com/OpenMOSS/MOSS-Image) that place panels, text and figures with **layout JSON** — numbers in, the same composition out. Free to use.

Write-up with all the measurements (Japanese): [pixelgnarly.com — MOSS-Imageの使い方](https://pixelgnarly.com/moss-image-comfyui/)

| Workflow | What it makes |
|---|---|
| [`moss_manga_irregular_panels.json`](workflows/moss_manga_irregular_panels.json) | A one-page manga with an irregular panel layout (1024×1536, seed 42). Over four seeds, panel edges land 3 px from the boxes on average; the same numbers written as plain text drift by 35 px. |
| [`moss_bonus_poolside_back.json`](workflows/moss_bonus_poolside_back.json) | An adult swimwear photoshoot, back view by a villa pool (1088×1536, seed 1601766429). One box per body part keeps the pose fixed across seeds. |

## Download

**[⬇ Download the ZIP](https://github.com/nigo-studio/moss-image-workflows/archive/refs/heads/main.zip)** — or use the green **Code** button and pick **Download ZIP**.

With git instead:

```bash
git clone https://github.com/nigo-studio/moss-image-workflows.git
```

## What you need

- **ComfyUI**, recent enough to ship the MOSS-Image nodes (tested on 0.42.0).
- Three model files from [Comfy-Org/MOSS-Image](https://huggingface.co/Comfy-Org/MOSS-Image), about 15.5 GB in total:

| Folder (under `models/`) | File |
|---|---|
| `diffusion_models` | `moss_image_sft_int8_convrot.safetensors` |
| `text_encoders` | `qwen3.5_9b_int8_convrot.safetensors` |
| `vae` | `flux2-vae.safetensors` |

- A GPU with 16 GB of VRAM (tested on an RTX 4070 Ti SUPER; about 14 GB in use, 12–14 s per 1 MP image).

## Use

Drag a `.json` from `workflows/` onto the ComfyUI canvas and press **Run**. With the same models and seed you should get the image from the write-up — we ran both files from the ComfyUI UI and got the same pixels.

Settings match the official ComfyUI template: 25 steps, CFG 2.5, `er_sde` / `simple`, Skip Layer Guidance on layer 22 for the first 50%.

### Keep the JSON out of the prompt box

The layout JSON sits in a **Text (Multiline)** node wired into `CLIPTextEncode`. Don't paste it straight into the `CLIPTextEncode` box: when you run from the UI, ComfyUI reads `{ }` there as dynamic-prompt syntax ("pick one at random") and strips the braces, and the layout silently stops working.

### Coordinates

Boxes are `[top, left, bottom, right]` in pixels with the image centre at 0 (`"bbox_coord_space": "abs_centralized"`, `"bbox_format": "yxyx"`). If you think in 0–1000 fractions of the canvas, convert with `fraction × size ÷ 1000 − size ÷ 2` (for a 1536 px tall image, 20 → −738). The `MOSS-Image Layout Prompt` node does this for you if you draw the boxes in ComfyUI instead.

## Let Claude Code set it up

Open your ComfyUI folder in Claude Code and paste:

```
Set up MOSS-Image in this ComfyUI install.

1. Update ComfyUI so the MOSS-Image nodes are available.
2. From Hugging Face Comfy-Org/MOSS-Image, put these three files in the right folders:
   - models/diffusion_models/moss_image_sft_int8_convrot.safetensors
   - models/text_encoders/qwen3.5_9b_int8_convrot.safetensors
   - models/vae/flux2-vae.safetensors
   About 15.5 GB in total. Check free disk space and GPU VRAM first, and stop and tell me if either is short.
3. Clone https://github.com/nigo-studio/moss-image-workflows and copy the two workflows into ComfyUI's user/default/workflows.
4. When done, tell me how to start ComfyUI and which workflow to open.
```

To reuse a layout for your own story, open this repo in Claude Code (a `CLAUDE.md` tells it how the JSON works) and ask, for example:

```
Use the JSON in moss_manga_irregular_panels.json as a template. Keep every panel bbox,
and swap the story for "lending an umbrella at a rainy station". Dialogue in Japanese,
10 characters or fewer per panel. All characters are adults.
```

## Notes

- Short Japanese text renders, but check every character before you use an image — small kanji break (審査員 → 審直員).
- Fingers and toes sometimes break. Run a few seeds and pick.
- The swimwear workflow names what to keep out (nudity, see-through fabric, anything that reads as underage) in the negative prompt instead of a blanket `nsfw`. Keep it that way if you edit it.

## 日本語

[MOSS-Image](https://github.com/OpenMOSS/MOSS-Image) で、JSONでコマ・文字・人物の位置を決めるComfyUIのワークフローです。無料で使えます。
測った数字や作例は、記事にまとめています: [pixelgnarly.com — MOSS-Imageの使い方](https://pixelgnarly.com/moss-image-comfyui/)

- `workflows/` の `.json` をComfyUIの画面にドラッグして実行。同じモデルとseedなら、記事の作例と同じ絵が出ます。
- JSONは「Text (Multiline)」ノードに入れて、線で `CLIPTextEncode` につないであります。`CLIPTextEncode` の欄に直接貼ると、画面から実行したときに `{ }` が消えて配置が効かなくなります。
- 導入をClaude Codeに任せるなら、上の「Let Claude Code set it up」の文をそのまま貼れます（英語のままで大丈夫です）。

## License

[MIT](LICENSE)

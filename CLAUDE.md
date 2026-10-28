# MOSS-Image Workflows

ComfyUI workflows for MOSS-Image (SFT, int8) that fix a composition with layout JSON. `README.md` is for humans; this file tells you how the files work.

## Layout

- `workflows/moss_manga_irregular_panels.json` — one-page manga, irregular panels, 1024×1536, seed 42
- `workflows/moss_bonus_poolside_back.json` — adult swimwear photoshoot, back view, 1088×1536, seed 1601766429

Both are flat graphs (no subgraphs): UNETLoader → SkipLayerGuidanceDiTSimple → KSampler; CLIPLoader (type `moss_image`) → CLIPTextEncode ×2; VAELoader → VAEDecode → SaveImage; EmptyFlux2LatentImage. The layout JSON is the value of the `PrimitiveStringMultiline` node (Text (Multiline)), linked into the positive CLIPTextEncode.

## The layout JSON

```json
{"summary": {"meta": ["manga"], "canvas": "single", "caption": "..."},
 "items": [{"label": "object", "caption": "...", "bbox": [top, left, bottom, right]},
           {"label": "text", "caption": "...", "text": "森の奥で、それは目を覚ました。", "bbox": [...]}],
 "bbox_coord_space": "abs_centralized", "bbox_format": "yxyx"}
```

- `summary.caption` describes the whole image. Each item is something placed in a box: `label: "object"` for things, `label: "text"` for lettering, with the exact characters in `text`.
- `bbox` is `[top, left, bottom, right]` in pixels, image centre at 0. Width W, height H: x runs −W/2…W/2, y runs −H/2…H/2.
- From a 0–1000 fraction: `floor(f × size ÷ 1000) − size ÷ 2` for top/left, `ceil(...)` for bottom/right.
- Captions are English. Text to draw goes in `text` in its own language.

## Rules when editing

- Keep the JSON in the Text (Multiline) node. Never move it into the CLIPTextEncode widget: the UI's dynamic-prompt pass strips `{ }` there and the layout breaks without an error.
- To reuse a layout, keep every `bbox` and change only captions and `text`. Change boxes only when the user asks for a different composition, and then keep the canvas size in sync with EmptyFlux2LatentImage.
- Keep text short (a phrase per box). Small or dense kanji break; tell the user to proofread every character.
- People are adults; say so in the captions. In the swimwear workflow keep the negative prompt as is — it names what must not appear (nudity, see-through fabric, underage cues) instead of relying on `nsfw`.
- Write the edited workflow as a new file next to the original; don't overwrite the originals.
- These files describe images; you don't run generations yourself unless the user asks and ComfyUI is running.

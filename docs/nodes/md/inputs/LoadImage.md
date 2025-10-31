# Load Image

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Loads an image file from the workspace input directory or via upload and outputs it as an image tensor along with a corresponding mask. If the source is multi-frame (e.g., animated GIF/PNG), frames of identical size are combined into a batch. The mask is derived from the image’s alpha channel when available; otherwise a blank mask is provided.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/inputs/LoadImage.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the start of image-processing workflows to bring external images into your pipeline. Select or upload a file from the input directory. The node outputs an image suitable for downstream processing and a mask that reflects image transparency.

## Inputs

<div style="overflow-x: auto;">
<table style="table-layout: fixed; width: 100%;">
<colgroup>
<col style="width: 15%;">
<col style="width: 10%;">
<col style="width: 15%;">
<col style="width: 30%;">
<col style="width: 30%;">
</colgroup>
<thead><tr><th>Field</th><th>Required</th><th>Type</th><th>Description</th><th>Example</th></tr></thead>
<tbody>
<tr><td style="word-wrap: break-word;">image</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The filename of the image to load from the input directory. Supports standard image formats and can accept uploads via the UI.</td><td style="word-wrap: break-word;">example.png</td></tr>
</tbody>
</table>
</div>

## Outputs

<div style="overflow-x: auto;">
<table style="table-layout: fixed; width: 100%;">
<colgroup>
<col style="width: 20%;">
<col style="width: 20%;">
<col style="width: 35%;">
<col style="width: 25%;">
</colgroup>
<thead><tr><th>Field</th><th>Type</th><th>Description</th><th>Example</th></tr></thead>
<tbody>
<tr><td style="word-wrap: break-word;">IMAGE</td><td style="word-wrap: break-word;">IMAGE</td><td style="word-wrap: break-word;">The loaded image (or batch of frames) normalized for processing.</td><td style="word-wrap: break-word;">An IMAGE tensor representing a single RGB image or a stacked batch of frames.</td></tr>
<tr><td style="word-wrap: break-word;">MASK</td><td style="word-wrap: break-word;">MASK</td><td style="word-wrap: break-word;">A mask derived from the image alpha channel; if no alpha channel exists, a blank mask is returned.</td><td style="word-wrap: break-word;">A MASK tensor aligned with the image (per-frame if animated).</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Multi-frame images (e.g., animated GIF/PNG) are returned as a batch when all frames share the same dimensions.
- Frames with mismatched dimensions are skipped to maintain a consistent batch size.
- If an alpha channel exists, the mask represents transparent areas of the image; if not, a blank mask is generated.
- Some multi-image formats (e.g., MPO) are treated as single-frame and will not produce a batch.
- Ensure the file is present in the input directory or use the UI upload option to make it selectable.

## Troubleshooting
- File not listed: Place the image in the input directory and refresh, or use the upload option in the UI.
- Unexpected batch output: The source file may be animated; if you need a single frame, choose a static image or extract a specific frame upstream.
- Missing transparency in mask: The source image may not have an alpha channel; supply an image with transparency if a non-blank mask is required.
- Inconsistent frame count: Frames with differing dimensions are ignored; ensure all frames in the source have identical width and height.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/inputs/LoadImage/Example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>


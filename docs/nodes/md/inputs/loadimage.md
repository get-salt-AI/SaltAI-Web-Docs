# Image

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Loads an image file from the configured input directory (or via upload) and outputs a normalized image along with an optional mask. Supports multi-frame formats (e.g., animated GIFs) by batching frames when sizes match and generates a mask from the image’s alpha channel (inverted, so 1 = masked area).

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/inputs/loadimage.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the start of a workflow to bring an external image into the pipeline. Commonly paired with encoders or preprocessors. Select a file from the input directory (or upload via the UI control) to get an IMAGE output (RGB, normalized 0–1) and a corresponding MASK output derived from transparency.

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
<tr><td style="word-wrap: break-word;">image</td><td>True</td><td style="word-wrap: break-word;">filename (selectable/uploadable)</td><td style="word-wrap: break-word;">The image file to load from the input directory. The UI shows a list of available files and also supports uploading a new image.</td><td style="word-wrap: break-word;">example.png</td></tr>
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
<tr><td style="word-wrap: break-word;">IMAGE</td><td style="word-wrap: break-word;">IMAGE</td><td style="word-wrap: break-word;">The loaded RGB image, normalized to 0–1. For multi-frame images with consistent dimensions, outputs a batch of frames.</td><td style="word-wrap: break-word;">A normalized image tensor suitable for downstream image processing nodes.</td></tr>
<tr><td style="word-wrap: break-word;">MASK</td><td style="word-wrap: break-word;">MASK</td><td style="word-wrap: break-word;">A mask derived from the image’s alpha channel, inverted so that 1 indicates masked (transparent) regions and 0 indicates unmasked (opaque) areas. If the source has no alpha, outputs an empty mask.</td><td style="word-wrap: break-word;">A mask aligned with the IMAGE output; for single-frame images, a single mask; for multi-frame, one mask per frame.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input location**: Files are read from the platform’s designated input directory; the widget also supports image upload.
- **Color and orientation**: Images are converted to RGB and auto-rotated according to EXIF data.
- **Normalization**: Pixel values are normalized to a 0–1 range.
- **Alpha handling**: If an alpha channel exists, the mask is generated as inverted alpha (1 = transparent/masked). If no alpha, an empty mask is produced.
- **Multi-frame support**: Multi-frame images (e.g., GIF) are batched only if all frames share the same dimensions; frames with mismatched sizes are skipped. MPO multi-frame batching is excluded.
- **Consistency requirement**: When batching multi-frame images, all included frames must have identical width and height.

## Troubleshooting
- **Selected file not listed**: Place the image in the input directory or use the upload control, then refresh the file list.
- **Unexpected empty mask**: The source image likely has no alpha channel. Provide an image with transparency or generate a mask using a separate node.
- **Multi-frame image outputs only one frame**: The format may be excluded from batching (e.g., MPO) or frames may have differing dimensions; ensure consistent frame sizes and a supported format.
- **Orientation looks wrong**: Verify the source image’s EXIF rotation. The node auto-applies EXIF transpose; if results seem off, re-export the image without problematic EXIF data.
- **Color/brightness appears different**: The node converts to RGB and normalizes to 0–1; downstream nodes must expect normalized RGB input.
- **Load error or corrupted output**: Ensure the file is a valid image and not corrupted; try re-saving the image in a standard format (PNG/JPG).

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/inputs/loadimage/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>


# Draw Mask

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Interactive node to draw a grayscale mask on a canvas and output it as a normalized tensor mask. It decodes the provided canvas image, resizes it to the specified dimensions, and ensures values are in the 0–1 range. If the canvas is empty (all black), it returns a zero mask with the requested size.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/inputs/drawmask.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need a hand-drawn mask for image operations such as inpainting, blending, or selective processing. Configure canvas size and brush parameters in the UI, draw on the canvas, and connect the resulting mask to downstream nodes that accept MASK inputs.

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
<tr><td style="word-wrap: break-word;">canvas</td><td>True</td><td style="word-wrap: break-word;">CANVAS</td><td style="word-wrap: break-word;">The drawn canvas image encoded as a data URL string. Represents the grayscale mask drawn in the UI.</td><td style="word-wrap: break-word;">data:image/png;base64,<base64-encoded-bytes></td></tr>
<tr><td style="word-wrap: break-word;">width</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Output mask width in pixels. Also controls the canvas aspect ratio in the UI.</td><td style="word-wrap: break-word;">512</td></tr>
<tr><td style="word-wrap: break-word;">height</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Output mask height in pixels. Also controls the canvas aspect ratio in the UI.</td><td style="word-wrap: break-word;">512</td></tr>
<tr><td style="word-wrap: break-word;">brush_size</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Brush diameter used in the drawing UI. Adjust to control stroke thickness.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">brush_color</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Brush intensity in grayscale from 0 (black) to 255 (white). Controls how strongly drawn areas contribute to the mask.</td><td style="word-wrap: break-word;">255</td></tr>
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
<tr><td style="word-wrap: break-word;">mask</td><td style="word-wrap: break-word;">MASK</td><td style="word-wrap: break-word;">A single-channel mask tensor with shape (1, height, width), normalized to 0–1. Matches the specified width and height.</td><td style="word-wrap: break-word;">A tensor-like mask with values between 0.0 (black) and 1.0 (white) at size 1x512x512</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Width and height must be between 32 and 2048 pixels; values outside this range are not allowed.
- Brush size must be between 1 and 100; brush color must be between 0 and 255.
- The mask is grayscale: 0 (black) indicates no mask, 1 (white) indicates full mask, intermediate values represent partial strength.
- If the canvas is entirely empty/black, an all-zero mask is produced with the requested dimensions.
- The input canvas is resized to the specified width and height, which may introduce smoothing.
- The canvas input must be a valid data URL (e.g., PNG) encoded as base64.

## Troubleshooting
- Mask appears all zero: Ensure you drew with a non-zero brush_color and that the canvas contains non-black content.
- Output size is unexpected: Confirm width and height are set as intended; the mask is always resized to these values.
- Edges look soft or blurry: Resizing the canvas to different dimensions can soften edges; draw at the target size to minimize resampling artifacts.
- Invalid canvas error or no output: Verify the canvas string is a properly formatted data URL (e.g., data:image/png;base64,<...>).
- Downstream node rejects mask shape: Ensure the receiving node expects a single-channel mask with shape (1, H, W).

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/inputs/drawmask/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>


# Draw Mask

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Interactive node to create a grayscale mask using a canvas. Produces a single-channel mask normalized between 0 and 1, where white is fully masked and black is unmasked. If the canvas is empty, it outputs a zero mask with the specified width and height.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/inputs/drawmask.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need a custom, hand-drawn mask for tasks like inpainting, compositing, or selectively applying effects. Configure the canvas size before drawing to match your target resolution, draw on the canvas with the chosen brush size and color, and pass the resulting mask to downstream nodes that accept a MASK input.

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
<tr><td style="word-wrap: break-word;">canvas</td><td>True</td><td style="word-wrap: break-word;">CANVAS</td><td style="word-wrap: break-word;">Interactive drawing surface input from the UI. Contains the strokes you draw to define the mask.</td><td style="word-wrap: break-word;">Interactive canvas data from the UI</td></tr>
<tr><td style="word-wrap: break-word;">width</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Output mask width in pixels. Also updates the canvas aspect ratio in the UI.</td><td style="word-wrap: break-word;">512</td></tr>
<tr><td style="word-wrap: break-word;">height</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Output mask height in pixels. Also updates the canvas aspect ratio in the UI.</td><td style="word-wrap: break-word;">512</td></tr>
<tr><td style="word-wrap: break-word;">brush_size</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Brush size used when drawing on the canvas in the UI.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">brush_color</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Brush color in grayscale for drawing on the canvas. 0 is black (unmasked), 255 is white (masked).</td><td style="word-wrap: break-word;">255</td></tr>
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
<tr><td style="word-wrap: break-word;">mask</td><td style="word-wrap: break-word;">MASK</td><td style="word-wrap: break-word;">Single-channel mask tensor of shape 1×H×W with values in [0, 1]. White areas (1.0) indicate masked regions; black areas (0.0) indicate unmasked regions.</td><td style="word-wrap: break-word;">A 1×512×512 mask tensor with values between 0 and 1</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Canvas size matters**: Set width and height to your intended final resolution before drawing to avoid resampling artifacts.
- **Empty canvas behavior**: If nothing is drawn, the node outputs a zero mask with the specified width and height.
- **Value range**: The mask is normalized to [0, 1], where 1.0 represents white (masked) and 0.0 represents black (unmasked).
- **Brush controls**: Brush size and brush color control the drawing behavior in the UI; they are not separate inputs to downstream nodes.
- **Resizing**: If the drawn canvas differs from the specified width/height, it is resized to match the target dimensions.

## Troubleshooting
- **Mask appears blank**: Ensure you are drawing with a nonzero brush color (e.g., 255 for white) and an adequate brush size.
- **Wrong mask size downstream**: Adjust this node's width and height to match the expected resolution of downstream nodes.
- **Mask looks soft or blurry**: Set the desired width and height first, then draw at that resolution to avoid resizing.
- **Mask is inverted**: Use white for areas you want masked and black for areas you want unmasked. If needed, invert in a downstream node.
- **Nothing changes after drawing**: Verify that the canvas input is being updated in the UI and that the node has been re-executed.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/inputs/drawmask/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>


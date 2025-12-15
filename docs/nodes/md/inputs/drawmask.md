# Draw Mask

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Interactive mask-drawing node that converts a user-drawn canvas into a single-channel grayscale mask. It outputs a normalized mask tensor sized to the specified width and height, with optional brush controls for an intuitive drawing experience.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/inputs/drawmask.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need a custom mask for tasks like inpainting, region selection, or compositing. Configure the target width/height to match downstream processing, draw on the canvas in the UI, and pass the resulting mask to nodes that accept a MASK input.

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
<tr><td style="word-wrap: break-word;">canvas</td><td>True</td><td style="word-wrap: break-word;">CANVAS</td><td style="word-wrap: break-word;">The interactive drawing surface input. Expects a data-URL image string from the UI representing the drawn mask in grayscale.</td><td style="word-wrap: break-word;">data:image/png;base64,<base64-encoded></td></tr>
<tr><td style="word-wrap: break-word;">width</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Output mask width in pixels. Also updates the canvas aspect ratio in the UI.</td><td style="word-wrap: break-word;">512</td></tr>
<tr><td style="word-wrap: break-word;">height</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Output mask height in pixels. Also updates the canvas aspect ratio in the UI.</td><td style="word-wrap: break-word;">512</td></tr>
<tr><td style="word-wrap: break-word;">brush_size</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Brush size used in the UI while drawing. Does not change output scaling; purely a drawing control.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">brush_color</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Brush intensity in grayscale (0 = black, 255 = white) used in the UI while drawing.</td><td style="word-wrap: break-word;">255</td></tr>
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
<tr><td style="word-wrap: break-word;">mask</td><td style="word-wrap: break-word;">MASK</td><td style="word-wrap: break-word;">A single-channel normalized mask tensor shaped (1, height, width), with values in [0.0, 1.0] derived from the drawn canvas.</td><td style="word-wrap: break-word;">MASK tensor with shape (1, 512, 512)</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Normalization**: The mask is normalized to [0.0, 1.0] from the grayscale canvas (0 = black, 1 = white).
- **Resizing**: The drawn canvas is resized to the specified width and height before output. Set width/height to match downstream nodes.
- **Empty mask handling**: If the canvas is fully empty (all pixels black), the node returns a zero mask with the specified width/height even if the canvas image size differs.
- **Brush controls**: brush_size and brush_color only affect the UI drawing experience; they do not alter the mask after it is drawn.
- **Input format**: The canvas must be a data-URL string containing a base64-encoded image.

## Troubleshooting
- **Mask dimensions don't match downstream expectations**: Ensure width and height here match what later nodes require.
- **Mask appears all black or all white**: Verify brush_color and drawing on the canvas; remember that 0 is black (0.0) and 255 is white (1.0).
- **No effect from changing brush_size/brush_color**: These settings only affect the drawing UI. Redraw after adjusting them.
- **Canvas not recognized**: Provide a valid data-URL image string (e.g., data:image/png;base64,<base64-encoded>).

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/inputs/drawmask/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>


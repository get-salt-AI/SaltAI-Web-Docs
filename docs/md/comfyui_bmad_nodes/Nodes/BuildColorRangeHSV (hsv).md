---
tags:
- Color
- HSVColorSpace
---

# BuildColorRangeHSV (hsv)
## Documentation
- Class name: `BuildColorRangeHSV (hsv)`
- Category: `Bmad/CV/Color A.`
- Output node: `False`

This node is designed to establish a range of HSV (Hue, Saturation, Value) colors based on provided lower and upper bounds. It adjusts these bounds to ensure they are within acceptable limits and determines the optimal hue mode for color range construction.
## Input types
### Required
- **`samples`**
    - Specifies the HSV samples from which the color range is derived, influencing the determination of lower and upper bounds.
    - Comfy dtype: `HSV_SAMPLES`
    - Python dtype: `HSV_Samples`
- **`percentage_modifier`**
    - A modifier that affects the interpolation of the HSV color range, influencing how the bounds are adjusted.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`interval_type`**
    - Determines the method used for calculating the color range, affecting the selection of lower and upper bounds and the hue mode.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
## Output types
- **`hsv_color`**
    - Comfy dtype: `HSV_COLOR`
    - The resulting HSV color range, including both lower and upper bounds, after adjustments.
    - Python dtype: `Tuple[HSV_COLOR, HSV_COLOR]`
- **`combo[string]`**
    - Comfy dtype: `COMBO[STRING]`
    - The determined optimal hue mode for the color range construction, based on the adjusted bounds.
    - Python dtype: `str`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class BuildColorRangeHSV:
    @staticmethod
    def percentile(samples: HSV_Samples, percentage):
        value = percentage / 100 / 2
        bounds = {}
        bounds["h"] = samples.h_quant2(.5 - value, .5 + value)
        bounds["s"] = samples.s_quant2(.5 - value, .5 + value)
        bounds["v"] = samples.v_quant2(.5 - value, .5 + value)
        return bounds

    @staticmethod
    def avg_3maxdev(samples: HSV_Samples, percentage):
        value = percentage / 100
        bounds = {}
        bounds["h"] = [samples.h_avg - samples.h_max_dev * 3 * value, samples.h_avg + samples.h_max_dev * 3 * value]
        bounds["s"] = [samples.s_avg - samples.s_max_dev * 3 * value, samples.s_avg + samples.s_max_dev * 3 * value]
        bounds["v"] = [samples.v_avg - samples.v_max_dev * 3 * value, samples.v_avg + samples.v_max_dev * 3 * value]
        return bounds

    @staticmethod
    def avg_2stddev(samples: HSV_Samples, percentage):
        value = percentage / 100
        bounds = {}
        bounds["h"] = [samples.h_avg - samples.h_std_dev * 2 * value, samples.h_avg + samples.h_std_dev * 2 * value]
        bounds["s"] = [samples.s_avg - samples.s_std_dev * 2 * value, samples.s_avg + samples.s_std_dev * 2 * value]
        bounds["v"] = [samples.v_avg - samples.v_std_dev * 2 * value, samples.v_avg + samples.v_std_dev * 2 * value]
        return bounds

    @staticmethod
    def median_interpolate(samples: HSV_Samples, percentage):
        value = percentage / 100
        bounds = {}
        bounds["h"] = Interval([samples.h_median, samples.h_median]).interpolate(value, [0, 179])
        bounds["s"] = Interval([samples.s_median, samples.s_median]).interpolate(value, [0, 255])
        bounds["v"] = Interval([samples.v_median, samples.v_median]).interpolate(value, [0, 255])
        return bounds

    interval_modes_map = {
        "median to extremes interpolation": median_interpolate,
        "average +- 3x max deviation": avg_3maxdev,
        "average +- 2x standard deviation": avg_2stddev,
        "sample percentage centered at median": percentile,
    }
    interval_modes = list(interval_modes_map.keys())

    @classmethod
    def INPUT_TYPES(cls):
        return {"required": {
            "samples": ("HSV_SAMPLES",),
            "percentage_modifier": ("INT", {"default": 50, "min": 1, "max": 100}),
            "interval_type": (cls.interval_modes, cls.interval_modes[0]),
        }}

    RETURN_TYPES = ("HSV_COLOR", "HSV_COLOR", InRangeHSV.hue_modes)
    FUNCTION = "get_interval"
    CATEGORY = f"{cv_category_path}/Color A."

    def get_interval(self, samples, percentage_modifier, interval_type):
        bounds = self.interval_modes_map[interval_type](samples, percentage_modifier)
        lower_bounds = np.array([bounds.get("h")[0], bounds.get("s")[0], bounds.get("v")[0]]).round()
        upper_bounds = np.array([bounds.get("h")[1], bounds.get("s")[1], bounds.get("v")[1]]).round()
        hue_mode = BuildColorRangeHSV.fix_bounds(lower_bounds, upper_bounds)
        return (upper_bounds, lower_bounds, hue_mode)

    @staticmethod
    def fix_bounds(lower_bounds, upper_bounds):
        # force hue bounds if interval >= 180
        interval_contains_zero = lower_bounds[0] <= 0  # example case: [-2, 2] includes the zero, but diff = 4
        if upper_bounds[0] - lower_bounds[0] >= (179 if interval_contains_zero else 180):
            lower_bounds[0] = 0
            upper_bounds[0] = 179  # note: return a color that exists, thus 179
        # check if hue needs to be split into 2 intervals when using inRange
        # note: 180 means zero is included, a one value split
        hue_mode = InRangeHSV.HUE_MODE_SPLIT \
            if lower_bounds[0] < 0 or upper_bounds[0] >= 180 \
            else InRangeHSV.HUE_MODE_SINGLE
        # correct hue bounds to [0, 180[
        lower_bounds[0] = (lower_bounds[0] + 180) % 180
        upper_bounds[0] = upper_bounds[0] % 180
        # clamp saturation and value limits to return actual colors in the outputs
        lower_bounds[1] = max(lower_bounds[1], 0)
        lower_bounds[2] = max(lower_bounds[2], 0)
        upper_bounds[1] = min(upper_bounds[1], 255)
        upper_bounds[2] = min(upper_bounds[2], 255)
        return hue_mode

```

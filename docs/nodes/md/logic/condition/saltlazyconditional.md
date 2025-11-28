# Lazy Conditional

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Evaluates up to 10 boolean conditions in order and returns the first corresponding value whose condition is true; otherwise returns the provided else value. Uses lazy evaluation so only the inputs needed for the selected branch are requested and computed.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/saltlazyconditional.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to build multi-branch logic without computing all branches. Provide condition1 with its value1, then optionally add more condition/value pairs (up to 10), and an else value for the fallback. The node short-circuits at the first true condition, requesting only the inputs it needs.

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
<tr><td style="word-wrap: break-word;">condition1</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">First condition to test. If true, value1 will be returned.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">value1</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to return if condition1 is true.</td><td style="word-wrap: break-word;">Result A</td></tr>
<tr><td style="word-wrap: break-word;">condition2</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Second condition, evaluated only if condition1 is false.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">value2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to return if condition2 is true.</td><td style="word-wrap: break-word;">Result B</td></tr>
<tr><td style="word-wrap: break-word;">condition3</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Third condition, evaluated only if prior conditions are false.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">value3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to return if condition3 is true.</td><td style="word-wrap: break-word;">Result C</td></tr>
<tr><td style="word-wrap: break-word;">condition4</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Fourth condition.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">value4</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to return if condition4 is true.</td><td style="word-wrap: break-word;">Result D</td></tr>
<tr><td style="word-wrap: break-word;">condition5</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Fifth condition.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">value5</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to return if condition5 is true.</td><td style="word-wrap: break-word;">Result E</td></tr>
<tr><td style="word-wrap: break-word;">condition6</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Sixth condition.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">value6</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to return if condition6 is true.</td><td style="word-wrap: break-word;">Result F</td></tr>
<tr><td style="word-wrap: break-word;">condition7</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Seventh condition.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">value7</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to return if condition7 is true.</td><td style="word-wrap: break-word;">Result G</td></tr>
<tr><td style="word-wrap: break-word;">condition8</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Eighth condition.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">value8</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to return if condition8 is true.</td><td style="word-wrap: break-word;">Result H</td></tr>
<tr><td style="word-wrap: break-word;">condition9</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Ninth condition.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">value9</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to return if condition9 is true.</td><td style="word-wrap: break-word;">Result I</td></tr>
<tr><td style="word-wrap: break-word;">condition10</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Tenth condition.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">value10</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to return if condition10 is true.</td><td style="word-wrap: break-word;">Result J</td></tr>
<tr><td style="word-wrap: break-word;">else</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fallback value if none of the conditions are true.</td><td style="word-wrap: break-word;">Default Result</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The selected value based on the first true condition, or the else value if none are true.</td><td style="word-wrap: break-word;">Result B</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Order matters**: conditions are evaluated from 1 to 10 and the first true one wins.
- **Lazy evaluation**: only the inputs necessary for the selected branch are requested and computed.
- **Provide pairs**: for any conditionX that can be true, ensure valueX is also provided; otherwise the node will request it.
- **Fallback recommended**: provide an else value to avoid a None output when no conditions are true.
- **Type flexibility**: value inputs are WILDCARD; ensure downstream nodes can handle the type that may be output.
- **Maximum branches**: supports up to 10 condition/value branches.

## Troubleshooting
- **Node keeps asking for inputs**: Ensure each conditionX that might be true has a corresponding valueX, and provide an else value.
- **Unexpected None output**: Add or connect an else value, or verify that at least one true condition has its value connected.
- **Downstream type errors**: Standardize the types of valueX and else so the selected output matches what downstream nodes expect.
- **Multiple true conditions choose the wrong branch**: Remember only the first true condition in order 1..10 is used. Reorder conditions accordingly.
- **No output changes when toggling conditions**: Verify that the condition you toggle precedes any other true condition; otherwise a prior true condition will short-circuit.

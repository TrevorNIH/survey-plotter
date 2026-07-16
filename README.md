# Survey Shot Plotter — prototype

Converts raw survey shot files (PNEZD text/CSV) into an interactive plot, a CAD-ready
DXF, and a printable sheet. Built for Brian Keever (P3 Civil) from the
2400009 / 161 Rainbow Dr, Stanley NC job as the reference dataset.

## How to run

Open `index.html` in any browser (double-click — no install, no server needed).
The Rainbow Dr sample loads automatically; use **Load file…** for a real job file.

## Input format

```
PointNumber,Northing,Easting,Elevation,Description
1008,5011.5685,10029.3695,101.3895,FN
```

## Field-code conventions the parser understands

- `B <CODE>` — begin a line (e.g. `B DECK`), `<CODE> ... E` — end it (`EC E`)
- Numeric suffixes are separate line instances: `FN` and `FN1` are two different fences
- One shot can serve several codes: `PP B OE` = power-pole point *and* start of overhead-electric line; `FN B FG` = fence shot that also opens a gate segment
- Unrecognized trailing words (`1"`, `#5`, `FACE @ CHIMNEY`) are kept as notes
- Shots more than 5,000 ft from the median (state-plane ties like the 2002/2199 NL/RBF pair) are auto-flagged as control and excluded from the plot/fit (toggleable)

Codes are editable in the sidebar (type point/line, layer, color) — unknown codes are
auto-added as point features so nothing is dropped silently.

## Views

- **CAD view** (screen): always true-north-up at 0°, pan/zoom, layer toggles — matches how Brian keeps his drawing files
- **Rotation slider**: rotates the *view only* for an upright, cleaner presentation; the compass/north arrow tracks the rotation so orientation is never lost
- **Print view / PDF**: browser print dialog produces the sheet — plot at a true engineering scale (1"=10…100 ft), north compass, scale bar, title block with editable job fields. Auto-centers on the subject lot (trimmed 10–90th percentile extent)

## DXF export

R12 ASCII DXF: `POLYLINE` per connected line (3D vertices with elevations),
`POINT` + `TEXT` per shot, layers with bright ACI colors mapped by hue.
World coordinates as shot (X=Easting, Y=Northing, Z=Elevation), **never rotated** —
the print rotation is presentation-only. Opens directly in AutoCAD/Civil 3D/Carlson.

## Known gaps / roadmap

1. No linetype styles in DXF yet (overhead electric should be DASHED, fence X-pattern, etc.)
2. No symbol blocks (well, power pole, monument symbols are circles for now)
3. Label placement is naive — dense clusters overlap; needs a declutter pass
4. Building lines could auto-close/square when begin≈end
5. No boundary/deed line entry (bearing-distance input) — needed to match the final mortgage-survey sheet with lot lines and setbacks
6. The DWG "plan of metrics" couldn't be read (compressed binary); ask Brian for a **DXF export** of it so layers/symbols/linetypes can be matched exactly
7. Only one sample dataset so far — more of Brian's jobs will surface new field codes; the code table is designed to absorb them

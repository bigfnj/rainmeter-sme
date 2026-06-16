# Rainmeter SME Reference — Part 2: Meters, Bangs, Lua, Plugins & Settings

> Compiled from https://docs.rainmeter.net/manual/

---

## PART 1: GENERAL METER OPTIONS

Available on **all** meter types:

| Option | Default | Description |
|---|---|---|
| `Meter` | (required) | Type: `Bar`, `String`, `Image`, `Histogram`, `Line`, `Rotator`, `Roundline`, `Button`, `Shape`. Cannot be changed dynamically. |
| `MeterStyle` | — | Pipe-delimited list of sections to inherit options from |
| `MeasureName`, `MeasureName2`... | — | Binds meter to one or more measures |
| `X`, `Y` | `0` | Position in pixels. Append `r` = relative to top/left of previous meter; `R` = relative to bottom/right of previous meter. |
| `W`, `H` | — | Width/height in pixels. |
| `Padding` | — | `Left,Top,Right,Bottom` in pixels. Background uses `SolidColor`. |
| `Hidden` | `0` | `1` = hide meter (W/H become zero; position still occupies space) |
| `Container` | — | Name of another meter to use as container/mask |
| `UpdateDivider` | `1` | Frequency multiplier. `-1` = update once only. |
| `OnUpdateAction` | — | Action each update |
| `SolidColor`, `SolidColor2` | `0,0,0,0` | Background color; gradient if both defined |
| `GradientAngle` | — | Gradient direction in degrees |
| `BevelType` | `0` | `0`=none, `1`=raised, `2`=sunken |
| `AntiAlias` | `0` | `1` = enable edge smoothing |
| `DynamicVariables` | `0` | `1` = re-evaluate variables each update |
| `TransformationMatrix` | `1;0;0;1;0;0` | 3×2 affine matrix: `scaleX;skewY;skewX;scaleY;moveX;moveY` |
| `Group` | — | Group name(s), pipe-delimited |

**Relative positioning:**
- `X=5r` → 5px to the right of the previous meter's left edge
- `X=5R` → 5px to the right of the previous meter's right edge (XW)
- `Y=5r` → 5px below previous meter's top edge
- `Y=5R` → 5px below previous meter's bottom edge (YH)

**MeterStyle:** Sections act as style templates. Options in the meter override the style. Styles can chain.

---

## PART 2: IMAGE OPTIONS (General)

Available on all meters that display image files. Supported formats: `.png`, `.jpg`, `.bmp`, `.gif` (no animation), `.tif`, `.webP`, `.ico`. Default extension if none specified: `.png`.

| Option | Default | Description |
|---|---|---|
| `ImagePath` | — | Folder containing the image |
| `ImageCrop` | — | `X,Y,W,H[,Origin]` — crop before resizing. Origin: 1=TL, 2=TR, 3=BR, 4=BL, 5=Center |
| `Greyscale` | `0` | `1` = desaturate image |
| `ImageTint` | `255,255,255,255` | RGBA tint. With Greyscale: recolor. Without: additive tint. |
| `ImageAlpha` | `255` | Opacity 0–255. Overrides alpha from ImageTint. |
| `ImageFlip` | `None` | `None`, `Horizontal`, `Vertical`, `Both` |
| `ImageRotate` | `0.0` | Rotation in degrees (before resize). Negative = counter-clockwise. |
| `UseExifOrientation` | `0` | `1` = auto-rotate based on EXIF data |
| `ColorMatrix1`–`ColorMatrix5` | Identity | 5×5 color transform, one row per option, semicolon-delimited. Overrides ImageTint/ImageAlpha. |

**ColorMatrix identity:**
```
ColorMatrix1=1;0;0;0;0
ColorMatrix2=0;1;0;0;0
ColorMatrix3=0;0;1;0;0
ColorMatrix4=0;0;0;1;0
ColorMatrix5=0;0;0;0;1
```
Rows 1–4: scale R/G/B/A. Row 5: add offset (0–1 range for rows 1–4; 0–255 for row 5 divided by 255).

---

## PART 3: CONTAINER OPTION

`Container=MeterName` makes another meter a container/mask for the current meter.

**Two effects:**
1. **Positional containment** — content positioned relative to container, constrained to container's W×H
2. **Pixel masking** — content only visible through solid pixels of container (enables shape masking, rounded corners, etc.)

**Rules:**
- Container meter itself is **not drawn** — only its content is visible
- First content meter auto-positions relative to container top-left
- Cannot nest containers (a meter cannot be both container and content)
- Any meter type can be container or content

---

## PART 4: STRING METER

```ini
[MeterClock]
Meter=String
MeasureName=MeasureTime
Text=%1
FontFace=Segoe UI
FontSize=24
FontColor=255,255,255,255
StringAlign=CenterCenter
AntiAlias=1
```

| Option | Default | Description |
|---|---|---|
| `Text` | `%1` | Text to display. `%1`, `%2`... = bound measure values. Supports variables, static text. |
| `FontFace` | `Arial` | Font family (installed or in `@Resources\Fonts`) |
| `FontSize` | `10.0` | Size in points |
| `FontColor` | `0,0,0,255` | Text color RGBA |
| `FontWeight` | — | 100–999 (100=Thin, 400=Regular, 700=Bold, 900=Black) |
| `StringAlign` | `Left` | `Left`/`Center`/`Right` + optional `Top`/`Center`/`Bottom` suffix. E.g. `CenterCenter`, `RightBottom` |
| `StringStyle` | `Normal` | `Normal`, `Bold`, `Italic`, `BoldItalic` |
| `StringCase` | `None` | `None`, `Upper`, `Lower`, `Proper` |
| `StringEffect` | `None` | `None`, `Shadow` (1px offset), `Border` (2px enlarged copy) |
| `FontEffectColor` | `0,0,0,255` | Color of StringEffect |
| `ClipString` | `0` | `0`=none, `1`=clip at W with ellipsis, `2`=auto expand/wrap to ClipStringW/H |
| `ClipStringW` | — | Max width for ClipString=2 |
| `ClipStringH` | — | Max height for ClipString=2 |
| `Angle` | `0.0` | Text rotation in radians |
| `Percentual` | `0` | `1` = display value as percentage |
| `NumOfDecimals` | `0` | Decimal places for numeric display |
| `Scale` | `1` | Divides measure value |
| `AutoScale` | `0` | `0`=off, `1`=1024-based (k/M/G/T), `1k`=1024 with kilo minimum, `2`=1000-based, `2k`=1000 with kilo minimum |
| `TrailingSpaces` | `0` | `1` = preserve leading/trailing spaces |

---

## PART 5: IMAGE METER

```ini
[MeterLogo]
Meter=Image
ImageName=#@#Images/logo.png
W=100
H=100
PreserveAspectRatio=1
```

| Option | Default | Description |
|---|---|---|
| `MeasureName` | — | Measure value defines the image file path |
| `ImageName` | `%1.png` | Image filename/path. `%1`/`%2` syntax for measure values. |
| `PreserveAspectRatio` | `0` | `0`=exact fit, `1`=fit within W/H, `2`=fill and crop |
| `ScaleMargins` | — | `Left,Top,Right,Bottom` — margins excluded from scaling |
| `Tile` | `0` | `1` = tile image within W×H |

> Hard-coded ImageName values are cached. Use `DynamicVariables=1` to reload on file change.

---

## PART 6: BAR METER

```ini
[MeterCPUBar]
Meter=Bar
MeasureName=MeasureCPU
BarColor=0,200,0,255
BarOrientation=Horizontal
W=200
H=12
```

| Option | Default | Description |
|---|---|---|
| `MeasureName` | (required) | Must return percentual value |
| `BarColor` | `0,128,0` | Color of the filled bar |
| `BarImage` | — | Image revealed proportionally to measure % |
| `BarBorder` | — | Pixels always drawn on each end when BarImage used |
| `BarOrientation` | `Vertical` | `Horizontal` or `Vertical` |
| `Flip` | `0` | `1` = reverse fill direction |

---

## PART 7: HISTOGRAM METER

Scrolling history graph.

| Option | Default | Description |
|---|---|---|
| `MeasureName` | (required) | Primary measure (percentual) |
| `MeasureName2` | — | Optional secondary measure |
| `PrimaryColor` | `0,128,0` | Primary histogram color |
| `SecondaryColor` | `255,0,0` | Secondary histogram color |
| `BothColor` | `255,255,0` | Overlap color |
| `PrimaryImage`, `SecondaryImage`, `BothImage` | — | Optional images instead of colors |
| `GraphStart` | `Right` | `Left` or `Right` |
| `GraphOrientation` | `Vertical` | `Horizontal` or `Vertical` |
| `Flip` | `0` | `1` = flip vertically |
| `AutoScale` | `0` | `1` = auto-scale to max value |

---

## PART 8: LINE METER

Connected line graph for one or more measures.

| Option | Default | Description |
|---|---|---|
| `LineCount` | `1` | Number of lines |
| `MeasureName`, `MeasureName2`... | — | One per line (percentual) |
| `LineColor`, `LineColor2`... | — | Color per line |
| `LineWidth` | `1` | Width in pixels |
| `Scale`, `Scale2`... | `1.0` | Multiplier per line |
| `AutoScale` | `0` | `1` = auto-scale to largest value |
| `HorizontalLines` | `0` | `1` = draw horizontal markers |
| `HorizontalLineColor` | `0,0,0,255` | Horizontal marker color |
| `GraphStart` | `Right` | `Left` or `Right` |
| `GraphOrientation` | `Vertical` | `Horizontal` or `Vertical` |
| `Flip` | `0` | `1` = flip |
| `TransformStroke` | `Normal` | `Normal`=scales with transforms, `Fixed`=stays at LineWidth |

---

## PART 9: ROTATOR METER

Rotates an image around a center point. Angles in **radians** (0 = right).

| Option | Default | Description |
|---|---|---|
| `ImageName` | — | Image file |
| `MeasureName` | — | Controls rotation angle |
| `OffsetX`, `OffsetY` | `0.0` | Center of rotation offset in pixels |
| `StartAngle` | `0.0` | Starting angle in radians |
| `RotationAngle` | `6.2832` | Total rotation range (2π = full circle). Negative = CCW. |
| `ValueRemainder` | `0` | Modulo: `43200`=12h clock, `3600`=minutes, `60`=seconds |

---

## PART 10: ROUNDLINE METER

Draws a rotating line or arc fill. Angles in **radians**.

| Option | Default | Description |
|---|---|---|
| `MeasureName` | — | Controls angle. Absent = static full circle. |
| `StartAngle` | — | Starting angle in radians |
| `RotationAngle` | — | Range in radians. Negative = CCW. |
| `LineStart` | — | Start distance from center (px) |
| `LineLength` | — | Total length from center (px) |
| `LineWidth` | `1` | Line width (ignored when Solid=1) |
| `LineColor` | `255,255,255,255` | Line/fill color |
| `Solid` | `0` | `1` = fill arc sector |
| `ControlAngle` | `1` | `1`=measure controls angle, `0`=static |
| `ControlStart` | `0` | `1` = measure controls start position |
| `StartShift` | `0` | Shift range for ControlStart (px) |
| `ControlLength` | `0` | `1` = measure controls length |
| `LengthShift` | `0` | Shift range for ControlLength (px) |
| `ValueRemainder` | — | Modulo for clock hands |

---

## PART 11: BUTTON METER

Three-state clickable button. W/H determined by image.

```ini
[MeterBtn]
Meter=Button
ButtonImage=#@#Images/btn.png   ; 3 horizontal frames: Normal|Pressed|Hover
ButtonCommand=[!Refresh]
X=10
Y=10
```

Image must have 3 frames stacked horizontally (or vertically). Frame 1=Normal, Frame 2=Pressed, Frame 3=Hover.
Click detection uses pixel transparency — transparent pixels are not clickable.

---

## PART 12: SHAPE METER

Vector graphics. Each shape defined with `Shape`, `Shape2`, ... using `|` to separate type from modifiers.

```ini
[MeterShape]
Meter=Shape
Shape=Rectangle 10,10,200,50,8 | Fill Color 50,50,50,200 | StrokeWidth 0
Shape2=Ellipse 50,50,40,40 | Fill LinearGradient MyGrad | Stroke Color 255,255,255,255 | StrokeWidth 2
LinearGradient MyGrad=135 | 0,0,0,255;0 | 100,100,100,255;0.5 | 0,0,0,255;1
```

### Shape Types

| Type | Parameters | Notes |
|---|---|---|
| `Rectangle` | `X,Y,W,H[,RadiusX][,RadiusY]` | Rounded corners if radius given |
| `Ellipse` | `CenterX,CenterY,RadiusX[,RadiusY]` | Circle if one radius |
| `Line` | `StartX,StartY,EndX,EndY` | Open shape |
| `Arc` | `StartX,StartY,EndX,EndY[,RadiusX,RadiusY,RotationAngle,SweepDir,ArcSize,ShapeEnd]` | SweepDir: 0=CW, 1=CCW. ArcSize: 0=small, 1=large. |
| `Curve` | `StartX,StartY,EndX,EndY,CtrlX1,CtrlY1[,CtrlX2,CtrlY2][,ShapeEnd]` | Quadratic (1 ctrl) or cubic Bézier (2 ctrl) |
| `Path OptionName` | Path data in named option | `StartX,StartY \| LineTo X,Y \| ArcTo ... \| ClosePath 0/1` |
| `Combine ShapeN` | `\| Union/Intersect/XOR/Exclude ShapeN` | Merge closed shapes |

### Attribute Modifiers

| Modifier | Default | Description |
|---|---|---|
| `Fill Color R,G,B,A` | `255,255,255,255` | Solid fill |
| `Fill LinearGradient Name` | — | Linear gradient fill |
| `Fill RadialGradient Name` | — | Radial gradient fill |
| `Stroke Color R,G,B,A` | `0,0,0,255` | Solid stroke |
| `Stroke LinearGradient/RadialGradient Name` | — | Gradient stroke |
| `StrokeWidth N` | `1` | Stroke width (half inside, half outside) |
| `StrokeStartCap / StrokeEndCap` | `Flat` | `Flat`, `Round`, `Square`, `Triangle` |
| `StrokeDashes` | — | `DashSize,GapSize,...` (multiples of StrokeWidth) |
| `StrokeLineJoin` | `Miter, 10.0` | `Miter`, `Bevel`, `Round`, `MiterOrBevel` |
| `StrokeDashOffset` | `0` | Starting offset |

### Transform Modifiers

| Modifier | Default | Description |
|---|---|---|
| `Rotate Deg[,AnchorX,AnchorY]` | `0` | Rotate around center or anchor |
| `Scale SX,SY[,AnchorX,AnchorY]` | `1.0,1.0` | Scale. `-1` = flip axis. |
| `Skew DX,DY[,AnchorX,AnchorY]` | `0.0,0.0` | Skew |
| `Offset X,Y` | `0,0` | Translate |
| `TransformOrder` | `Rotate,Scale,Skew,Offset` | Application order |

### Gradients

**Linear:**
```ini
LinearGradient GradName=Angle | Color1;Stop1 | Color2;Stop2 | ...
; Angle: 0=R→L, 90=B→T, 180=L→R, 270=T→B
; Stop: 0.0–1.0
```

**Radial:**
```ini
RadialGradient GradName=CenterX,CenterY[,OffsetX,OffsetY,RadiusX,RadiusY] | Color;Stop | ...
```

Use `LinearGradient1` / `RadialGradient1` for alternate gamma-corrected interpolation.

Mouse hit detection on shapes uses solid pixels only — transparent areas pass clicks through.

### Extend Modifier
`Extend OptionName1,OptionName2` — references named options containing shared modifiers. Cannot cascade.

---

## PART 13: MOUSE ACTIONS

Available on any meter or in `[Rainmeter]` section. Meter actions override skin-level actions.

### Click Actions
```ini
LeftMouseUpAction=[!Refresh]
LeftMouseDownAction=...        ; disables skin dragging!
LeftMouseDoubleClickAction=...
RightMouseUpAction=...         ; disables context menu
MiddleMouseUpAction=...
X1MouseUpAction=...            ; mouse button 4
X2MouseUpAction=...            ; mouse button 5
```

### Hover Actions
```ini
MouseOverAction=[!SetOption MeterText FontColor "255,255,0,255"][!UpdateMeter *][!Redraw]
MouseLeaveAction=[!SetOption MeterText FontColor "255,255,255,255"][!UpdateMeter *][!Redraw]
```

### Scroll Actions
```ini
MouseScrollUpAction=[!CommandMeasure MeasureVolume "ChangeVolume 5"]
MouseScrollDownAction=[!CommandMeasure MeasureVolume "ChangeVolume -5"]
MouseScrollLeftAction=...
MouseScrollRightAction=...
```

### Cursor Options
| Option | Default | Description |
|---|---|---|
| `MouseActionCursor` | `1` | Show hand cursor on hover over clickable meters |
| `MouseActionCursorName` | — | Custom cursor from `@Resources\Cursors\`. Built-ins: `HAND`, `TEXT`, `HELP`, `BUSY`, `CROSS`, `PEN`, `NO`, `SIZE_ALL`, `SIZE_NESW`, `SIZE_NS`, `SIZE_NWSE`, `SIZE_WE`, `UPARROW`, `WAIT`. Accepts `.cur` or `.ani`. |

---

## PART 14: BANGS

Bangs are action commands. Syntax: `[!BangName Param1 Param2]` or `[!BangName "Param with spaces" "Param2"]`
Most accept optional `Config` parameter. `*` = all loaded skins. Omit = current skin.

### Application Bangs
| Bang | Description |
|---|---|
| `!About [Tab]` | Open About. Tabs: `Log`, `Skins`, `Plugins`, `Version` |
| `!Manage [Tab,Config,File]` | Open Manage. Tabs: `Skins`, `Layouts`, `GameMode`, `Settings` |
| `!Log "Message"[,Type]` | Log. Types: `Notice`, `Error`, `Warning`, `Debug` |
| `!TrayMenu` | Open tray context menu at cursor |
| `!RefreshApp` | Full refresh + rescan skin folder |
| `!ResetStats` | Reset network statistics |
| `!LoadLayout "Name"` | Load named layout |
| `!Quit` | Quit Rainmeter |
| `!SetClip "String"` | Copy to clipboard |
| `!SetWallpaper "File"[,"Position"]` | Set wallpaper. Positions: Center/Tile/Stretch/Fit/Fill/Span |

### Skin Bangs
| Bang | Description |
|---|---|
| `!Refresh [Config]` | Reload skin from file |
| `!Update [Config]` | Immediately update all measures+meters |
| `!Redraw [Config]` | Immediately redraw (use after UpdateMeasure/UpdateMeter) |
| `!Show/Hide/Toggle [Config]` | Show/hide/toggle skin |
| `!ShowFade/HideFade/ToggleFade [Config]` | Fade show/hide |
| `!Move "X,Y" [Config]` | Move skin window |
| `!SetWindowPosition "WindowX,WindowY[,AnchorX,AnchorY]" [Config]` | Set position with modifier support |
| `!ActivateConfig "Config" [File]` | Activate/load skin |
| `!DeactivateConfig [Config]` | Deactivate/unload skin |
| `!ToggleConfig "Config" [File]` | Toggle active/inactive |
| `!SetTransparency "Alpha" [Config]` | Transparency 0–255 |
| `!ZPos "Position" [Config]` | Z-position: -2=Desktop, -1=Bottom, 0=Normal, 1=Top, 2=Always on Top |
| `!Draggable "Setting" [Config]` | 0=off, 1=on, -1=toggle |
| `!ClickThrough "Setting" [Config]` | 0=off, 1=on, -1=toggle |
| `!KeepOnScreen "Setting" [Config]` | 0=off, 1=on, -1=toggle |
| `!SnapEdges "Setting" [Config]` | 0=off, 1=on, -1=toggle |
| `!AutoSelectScreen "Setting" [Config]` | 0=off, 1=on, -1=toggle |
| `!EditSkin [Config,File]` | Open skin in text editor |
| `!Delay [ms]` | Delay execution (min 16ms; avoid in complex use) |
| `!FadeDuration "ms" [Config]` | Set fade duration |

### Measure Bangs
| Bang | Description |
|---|---|
| `!EnableMeasure "Measure" [Config]` | Enable measure |
| `!DisableMeasure "Measure" [Config]` | Disable measure |
| `!ToggleMeasure "Measure" [Config]` | Toggle enable/disable |
| `!PauseMeasure "Measure" [Config]` | Pause (freeze value) |
| `!UnpauseMeasure "Measure" [Config]` | Unpause |
| `!TogglePauseMeasure "Measure" [Config]` | Toggle pause |
| `!UpdateMeasure "Measure" [Config]` | Force immediate update |
| `!CommandMeasure "Measure" "Command" [Config]` | Send command to plugin/script |

### Meter Bangs
| Bang | Description |
|---|---|
| `!ShowMeter/!HideMeter/!ToggleMeter "Meter" [Config]` | Show/hide/toggle meter |
| `!UpdateMeter "Meter" [Config]` | Update meter (use `*` for all). Must `!Redraw` after. |
| `!MoveMeter "X,Y,Meter" [Config]` | Move meter |
| `!ShowMeterGroup/!HideMeterGroup/!ToggleMeterGroup/!UpdateMeterGroup "Group" [Config]` | Act on group |

### Option / Variable Bangs
| Bang | Description |
|---|---|
| `!SetOption "Section" "Option" "Value" [Config]` | Set option dynamically |
| `!SetOptionGroup "Group" "Option" "Value" [Config]` | Set option on all in group |
| `!SetVariable "Var" "Value" [Config]` | Set variable in memory |
| `!SetVariableGroup "Var" "Value" "Group"` | Set variable across group |
| `!WriteKeyValue "Section" "Key" "Value" [FilePath]` | Write permanently to INI |

### Mouse Action State Bangs
Three states: **Enabled** (fires normally), **Disabled** (detected, no action), **Cleared** (not detected — click-through).

```
!DisableMouseAction "Meter" "MouseAction(s)" [Config]
!ClearMouseAction "Meter" "MouseAction(s)" [Config]
!EnableMouseAction "Meter" "MouseAction(s)" [Config]
!ToggleMouseAction "Meter" "MouseAction(s)" [Config]
```

`MouseAction(s)` = comma-separated list of action names, or `*` for all.
Group variants: `!DisableMouseActionGroup`, `!DisableMouseActionSkinGroup` etc.

### Sound (not bangs — no `!`)
```
Play "SoundFile.wav"
PlayLoop "SoundFile.wav"
PlayStop
```

---

## PART 15: LUA SCRIPTING

Rainmeter uses **Lua 5.1**. Scripts run via `Measure=Script`.

```ini
[MeasureScript]
Measure=Script
ScriptFile=#@#Scripts/MyScript.lua
MyOption=Hello
```

### Reserved Lua Functions
```lua
function Initialize()
    -- Called once on skin load/refresh (even if disabled)
    -- Setup here
end

function Update()
    -- Called each update cycle
    -- return number, "string"   → sets measure's number and string value
    -- return "string"           → number=0, string="string"
    -- return 42                 → number=42, string="42"
    -- return                    → number=0, string=""
end
```

### SKIN Object Functions
```lua
SKIN:GetMeasure("MeasureName")     -- returns measure object or nil
SKIN:GetMeter("MeterName")         -- returns meter object or nil
SKIN:GetVariable("Name"[,"Default"])
SKIN:GetX() / GetY() / GetW() / GetH()
SKIN:MoveWindow(x, y)
SKIN:FadeWindow(from, to)          -- 0–255
SKIN:Bang("!BangName"[, arg1, arg2, ...])
SKIN:MakePathAbsolute("relative/path")
SKIN:ReplaceVariables("string with #vars#")
SKIN:ParseFormula("(2 + 2)")       -- returns number or nil; must have parentheses
```

### Measure Object Functions
```lua
local m = SKIN:GetMeasure("MeasureName")
m:GetValue()           -- number value
m:GetStringValue()     -- string value
m:GetOption("Name"[, "Default"[, bReplaceMeasures]])
m:GetNumberOption("Name"[, Default])
m:GetName()            -- section name
m:GetMinValue()
m:GetMaxValue()
m:GetRelativeValue()   -- 0.0–1.0
m:GetValueRange()      -- MaxValue - MinValue
m:Disable()
m:Enable()
```

### Meter Object Functions
```lua
local mt = SKIN:GetMeter("MeterName")
mt:GetOption("Name"[, "Default"[, bReplaceMeasures]])
mt:GetName()
mt:GetX([Absolute]) / GetY([Absolute])
mt:GetW() / GetH()
mt:SetX(val) / SetY(val) / SetW(val) / SetH(val)
mt:Hide() / Show()
```

### SELF Object
Pre-created reference to the Script measure itself. Supports all measure object functions.

### CommandMeasure Integration
```ini
LeftMouseUpAction=[!CommandMeasure "MeasureScript" "DoSomething('arg', 42)"]
```
```lua
function DoSomething(strArg, numArg)
    SKIN:Bang("!SetVariable", "MyVar", strArg)
end
```
Use single quotes for Lua strings inside Rainmeter bang strings.

### Inline Lua
Call Lua functions inline anywhere section variables are allowed:
```ini
Text=[&MeasureScript:GetFormattedValue(42, 'prefix')]
DynamicVariables=1    ; required
```

Access global Lua variables: `[&MeasureScript:myGlobalVar]`

Nesting syntax in inline Lua: `[#VarName]` = `#VarName#`, `[&MeasureName]` = measure value

### Include External Files
```lua
dofile(SKIN:GetVariable('@') .. 'Scripts/Helpers.lua')
```

### Restrictions
No `require`, `os.exit`, `os.setlocale`, `io.popen`, `collectgarbage`, no external compiled libs.

### Logging
```lua
print("Debug message")  -- appears in About > Log
```

---

## PART 16: PLUGINS — INPUTTEXT

Creates floating text input boxes.

```ini
[MeasureInput]
Measure=Plugin
Plugin=InputText
Command1=[!SetVariable UserSearch "$UserInput$"][!CommandMeasure MeasureWebSearch "Update"]
DefaultValue=Search...
W=200
H=24
FontSize=12
SolidColor=30,30,30,255
FontColor=255,255,255,255
X=10
Y=10
FocusDismiss=1
```

Triggered: `!CommandMeasure "MeasureInput" "ExecuteBatch ALL"` (or `"ExecuteBatch 1"` or `"ExecuteBatch 1-3"`)

| Option | Default | Description |
|---|---|---|
| `Command1`, `Command2`... | — | Actions on submit. `$UserInput$` = typed value. |
| `DefaultValue` | `''` | Pre-filled text |
| `Password` | `0` | `1` = show asterisks |
| `InputLimit` | `0` | Max characters |
| `InputNumber` | `0` | `1` = numeric only |
| `X`, `Y`, `W`, `H` | — | Position/size (no `r`/`R` relative positioning) |
| `SolidColor` | `255,255,255,255` | Background color |
| `FontColor` | `0,0,0` | Text color |
| `FontFace`, `FontSize`, `StringStyle`, `StringAlign` | — | Font options |
| `TopMost` | — | `1`=stay on top |
| `FocusDismiss` | `1` | `1`=click outside dismisses |
| `OnDismissAction` | — | Action when dismissed without Enter |

Incompatible with `AlwaysOnTop=2` skins.

---

## PART 17: PLUGINS — WIN7AUDIO

```ini
[MeasureVolume]
Measure=Plugin
Plugin=Win7AudioPlugin
```

- **String value:** Current audio device name
- **Number value:** Volume percentage (0–100)

| Command | Description |
|---|---|
| `"ToggleNext"` | Cycle to next output device |
| `"TogglePrevious"` | Cycle to previous output device |
| `"SetOutputIndex N"` | Set device by index |
| `"ToggleMute"` | Toggle mute |
| `"SetVolume N"` | Set to N% (0–100), disables mute |
| `"ChangeVolume N"` | Adjust by N% (negative = lower) |

---

## PART 18: GLOBAL SETTINGS (Rainmeter.ini)

Stored in `%APPDATA%\Rainmeter\Rainmeter.ini`.

### [Rainmeter] Section

| Option | Default | Description |
|---|---|---|
| `SkinPath` | — | Path to skins folder |
| `ConfigEditor` | `Notepad` | Text editor path |
| `TrayIcon` | `1` | `0` = remove tray icon |
| `TrayExecuteM/R/DM/DR` | — | Actions for tray icon clicks |
| `DesktopWorkArea` | — | `X,Y,Width,Height` for maximized window area |
| `DesktopWorkAreaType` | `0` | `0`=absolute rect, `1`=margins from edges |
| `Logging` | `0` | `1` = save log to file |
| `Debug` | `0` | `1` = verbose logging |
| `DisableVersionCheck` | `0` | `1` = disable update check |
| `DisableDragging` | `0` | `1` = prevent all skin dragging |
| `NormalStayDesktop` | `1` | `1` = correct Z-order on Win+D |
| `DisableRDP` | `0` | `1` = no redraws during RDP |
| `Language` | `1033` | LCID language code |
| `HardwareAcceleration` | `0` | `1` = GPU acceleration |

### Per-Skin [SkinConfig] Sections

| Option | Default | Description |
|---|---|---|
| `Active` | — | `0`=inactive; nonzero=active variant index |
| `WindowX` | — | X position. Suffixes: `%`=percent, `R`=from right, `@N`=monitor N, formulas in `()` |
| `WindowY` | — | Y position. Suffixes: `%`, `B`=from bottom, `@N`, formulas |
| `AnchorX` | — | Anchor X offset within skin. Suffixes: `%`, `R`, formulas |
| `AnchorY` | — | Anchor Y offset. Suffixes: `%`, `B`, formulas |
| `SavePosition` | `1` | `1` = save position changes |
| `AlwaysOnTop` | `0` | `2`=Stay Topmost, `1`=Topmost, `0`=Normal, `-1`=Bottom, `-2`=On Desktop |
| `Draggable` | `1` | `1` = draggable |
| `SnapEdges` | `1` | `1` = snap to edges (CTRL to temp disable) |
| `StartHidden` | `0` | `1` = start hidden |
| `AlphaValue` | `255` | Transparency 0–255 |
| `OnHover` | `0` | `0`=nothing, `1`=hide, `2`=fade in, `3`=fade out |
| `FadeDuration` | `250` | Fade duration in ms |
| `ClickThrough` | `0` | `1` = clicks pass through |
| `KeepOnScreen` | `1` | `1` = constrain to screen |
| `LoadOrder` | `0` | Integer; lower = loaded first |
| `AutoSelectScreen` | `0` | `1` = dynamically set monitor |
| `Group` | — | Group name(s), pipe-separated |
| `DragGroup` | — | Drag Group name(s) |

**Centering formula:**
```ini
WindowX=(#WORKAREAX# + (#WORKAREAWIDTH# / 2) - (SkinWidth / 2))
WindowY=(#WORKAREAY# + (#WORKAREAHEIGHT# / 2) - (SkinHeight / 2))
```

---

## PART 19: DISTRIBUTING SKINS

### .rmskin Package

Use Skin Packager (Manage → Create .rmskin). Cannot rename a `.zip` to `.rmskin`.

| Field | Notes |
|---|---|
| Name, Author | Required |
| Version | Optional string/number/date |
| Add skin (root config folder) | One only |
| Add layout | Optional |
| Add plugin | Must include both 32-bit AND 64-bit DLLs |
| Load skin after install | Specify `.ini` file |
| Load layout after install | Replaces user layout |
| Min. Rainmeter version | e.g. `4` for any 4.x |
| Min. Windows version | e.g. Windows 7 |
| Header image | `.bmp`, exactly **400×60 pixels** |
| Variables files | Relative paths to preserve on reinstall. Pipe-separated. |
| Merge skins | Don't delete existing files before install (for patches/expansions) |

Skin Packager ignores hidden files/folders — use to exclude dev files.

---

## PART 20: TOOLTIPS

Available on any meter:

```ini
ToolTipText=This is a tooltip
ToolTipTitle=Title
ToolTipIcon=Info     ; Info, Warning, Error, Question, or path to .ico
ToolTipType=0        ; 0=standard balloon, 1=rich text
ToolTipWidth=300     ; max width in pixels
ToolTipHidden=0      ; 1=hide this meter's tooltip
```

---

## PART 21: KNOWN LIMITATIONS & GOTCHAS

1. **No real-time variables in [Rainmeter]** — the `[Rainmeter]` section only reads variables at skin load. Use DynamicVariables on meters/measures instead.
2. **Order matters** — measures update top-to-bottom. A measure referencing another that hasn't run yet gets last cycle's value.
3. **WebParser UpdateRate** — changes to URL/RegExp via SetOption don't take effect until next UpdateRate cycle. Follow with `!CommandMeasure "Parent" "Update"` to force.
4. **InputText incompatible with AlwaysOnTop=2** — use AlwaysOnTop=1 or lower.
5. **LeftMouseDownAction kills dragging** — use LeftMouseUpAction for click actions that need dragging preserved.
6. **RightMouseAction kills context menu** — any right-click action on a meter disables context menu for that meter.
7. **CreateProcessAsUser / Session 0** — Rainmeter runs in user session; no special process elevation needed.
8. **Button meter ignores W/H** — size is determined entirely by image dimensions.
9. **DynamicVariables=1 required** for section variables `[MeasureName]` in Text/options.
10. **Lua 5.1 only** — not 5.2+. No `require`, no external compiled modules.
11. **ImageName is cached** — hard-coded image paths are cached at load. Need `DynamicVariables=1` to reload changed images.
12. **Shape hit detection uses pixels** — transparent regions on Shape meters pass mouse events through.
13. **Windows 10+ Hybrid Shutdown** resets uptime differently — use SecondsValue with login time for accurate user session uptime.
14. **NetIn/Out measures LAN traffic** — includes all NIC activity, not just internet. Use Interface=Best for auto-selection.
15. **Max 99 WebParser StringIndex captures** per parent measure.

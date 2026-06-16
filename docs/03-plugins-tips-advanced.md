# Rainmeter — Plugins, Tips & Advanced Reference

Covers: IfConditions, Substitute, RunCommand, Power, Quote, FileView, FolderInfo plugins; Fonts, ColorMatrix, WebParser tutorials; @Include, @Resources, Tooltips, Tips index.

---

## IfConditions (General Measure Option)

Evaluates a mathematical formula as true/false and executes actions. Applies to any measure type.

**Important:** IfCondition can only evaluate numeric formulas. For string testing, use `IfMatchActions`.

### Options

| Option | Notes |
|---|---|
| `IfCondition` / `IfCondition2` ... `IfConditionN` | Formula to evaluate |
| `IfTrueAction` / `IfTrueAction2` ... | Action when condition becomes true (fires once per transition) |
| `IfFalseAction` / `IfFalseAction2` ... | Action when condition becomes false (fires once per transition) |
| `IfConditionMode` | `0` (default) = fires once on transition; `1` = fires every update while condition holds |

**Formula operators:** `<>`, `=`, `<`, `>`, `<=`, `>=`, `&&`, `||`  
Compound expressions: each side of `&&` / `||` must be in parentheses.

```ini
IfCondition=MeasureCPU >= 10
IfCondition2=(MeasureOne > 5) && (MeasureOne < 10)
IfCondition3=(MeasureName = 25) || (MeasureName = 50)
```

**Warning:** Avoid `!Refresh`, `!Update`, `!UpdateMeasure #CURRENTSECTION#` inside actions when `IfConditionMode=1` — causes infinite loops.

```ini
[MeasureCPU]
Measure=CPU
IfCondition=MeasureCPU < 10
IfTrueAction=[!SetOption MeterLabel Text "Low"]
IfCondition2=(MeasureCPU >= 10) && (MeasureCPU <= 90)
IfTrueAction2=[!SetOption MeterLabel Text "Normal"]
IfCondition3=MeasureCPU > 90
IfTrueAction3=[!SetOption MeterLabel Text "High!"]
OnUpdateAction=[!UpdateMeter MeterLabel][!Redraw]
```

---

## Substitute (General Measure Option)

Replaces parts of a measure's string value before display. Does not affect the numeric value.

### Options

| Option | Notes |
|---|---|
| `Substitute` | Comma-delimited `"pattern":"replacement"` pairs, processed in order |
| `RegExpSubstitute` | `0` (default) or `1` to use Perl-compatible regex in patterns |

**Rules:**
- Multiple pairs: `"10":"Ten","1":"One"` — order matters; put longer patterns first
- Single quotes can be used on either side when the other side contains double quotes
- With `RegExpSubstitute=1`: captures referenced as `\1`, `\2`, ...; `\0` = entire match

```ini
[MeasureYear]
Measure=Time
Format=%Y
Substitute="2024":"Twenty Twenty-Four","2025":"Twenty Twenty-Five"

[MeasureEx]
Measure=String
String=192.168.1.101
RegExpSubstitute=1
Substitute="^(\d{1,3}).(\d{1,3}).(\d{1,3}).\d{1,3}$":"\1.\2.\3.***"
; Result: 192.168.1.***
```

---

## RunCommand Plugin

`Plugin=RunCommand`

Executes an external command, captures STDOUT as the measure's string value.

**Advantages over standard action commands:**
- Runs with hidden window (no cmd.exe flash)
- Captures STDOUT output as the measure value
- Detects completion, error codes, and timeout

**Trigger:** `[!CommandMeasure MeasureName "Run"]` — plugin does nothing until explicitly triggered.

**Number value:**
- `-1` = not yet run
- `0` = running
- `1` = success
- `100`–`106` = error codes

### Options

| Option | Default | Notes |
|---|---|---|
| `Program` | `%ComSpec% /U /C` | Executable. Default = cmd.exe with Unicode pipe |
| `Parameter` | None | Command-line args. When no Program: entire command run in cmd.exe |
| `State` | `Hide` | `Hide`, `Show`, `Minimized`, `Maximized` |
| `FinishAction` | None | Bangs/actions on program exit |
| `OutputFile` | None | Path to save STDOUT output |
| `OutputType` | `UTF16` | `UTF16`, `UTF8`, `ANSI` — must match program output encoding |
| `StartInFolder` | Skin folder | Working directory for the program |
| `Timeout` | `-1` | Milliseconds before auto-kill. `-1` = no timeout |

**Note:** Quote programs with spaces: `Program=""C:\Program Files\App\app.exe""`

### Commands

| Command | Effect |
|---|---|
| `Run` | Execute the program |
| `Close` | Send close signal (program may prompt) |
| `Kill` | Terminate immediately |

### Error Codes

| Code | Meaning |
|---|---|
| 100 | Unknown command |
| 101 | Program already running |
| 102 | Program not running (for Close/Kill) |
| 103 | Cannot start program (bad path) |
| 104 | Cannot save OutputFile |
| 105 | Cannot terminate process |
| 106 | Cannot create pipe |

```ini
[MeasureRun]
Measure=Plugin
Plugin=RunCommand
Program=PowerShell
Parameter=(Get-CimInstance -ClassName Win32_Processor -Property Name).Name
State=Hide
OutputType=ANSI
RegExpSubstitute=1
Substitute="\s+#CRLF#":""
FinishAction=[!UpdateMeter MeterResult][!Redraw]

[MeterRun]
Meter=String
Text=Click to Run
LeftMouseUpAction=[!CommandMeasure MeasureRun "Run"]

[MeterResult]
Meter=String
MeasureName=MeasureRun
```

---

## Power Plugin

`Plugin=PowerPlugin`

Measures battery and CPU frequency information.

### Options

| Option | Valid Values | Notes |
|---|---|---|
| `PowerState` | `ACLine` | `0` = on battery, `1` = plugged in |
| | `Status` | `0`=no battery, `1`=charging, `2`=critical, `3`=low, `4`=above low |
| | `Status2` | Raw `BatteryFlag` from Windows `SYSTEM_POWER_STATUS` |
| | `Lifetime` | Remaining battery time (use `Format` to style) |
| | `Percent` | Battery percentage (0–100) |
| | `Hz` | CPU rated frequency in Hz |
| | `MHz` | CPU rated frequency in MHz |
| `Format` | `%H:%M` | Time format for `Lifetime` (same syntax as Time measure) |

```ini
[MeasureBatteryPercent]
Measure=Plugin
Plugin=PowerPlugin
PowerState=Percent

[MeasureBatteryTime]
Measure=Plugin
Plugin=PowerPlugin
PowerState=Lifetime
Format=%H:%M

[MeasureACLine]
Measure=Plugin
Plugin=PowerPlugin
PowerState=ACLine

[MeterBattery]
Meter=String
MeasureName=MeasureBatteryPercent
Text=Battery: %1%
```

---

## Quote Plugin

`Plugin=QuotePlugin`

Returns a random line from a text file OR a random file path from a folder.

### Options

| Option | Default | Notes |
|---|---|---|
| `PathName` | — | Path to a file or folder |
| `Separator` | `#CRLF#` | Delimiter for splitting file contents (file mode only) |
| `Subfolders` | `1` | Include subfolders (folder mode) |
| `FileFilter` | — | Semicolon-delimited extension filter: `*.jpg;*.png;*.gif` |

**File mode:** PathName points to a text file → returns a random delimited segment  
**Folder mode:** PathName points to a directory → returns a random matching file's full path

```ini
; Random quote from text file
[MeasureQuote]
Measure=Plugin
Plugin=QuotePlugin
PathName=#CURRENTPATH#quotes.txt
Separator=#CRLF#
UpdateDivider=60

; Random image from folder
[MeasureRandomImage]
Measure=Plugin
Plugin=QuotePlugin
PathName=%HOMEDRIVE%%HOMEPATH%\Pictures\
Subfolders=1
FileFilter=*.jpg;*.png;*.gif
UpdateDivider=30

[MeterRandomImage]
Meter=Image
MeasureName=MeasureRandomImage
W=200
PreserveAspectRatio=1
```

---

## FileView Plugin

`Plugin=FileView`

Reads folder contents: names, sizes, dates, icons. Uses parent/child measure pattern.

**Important:** Does NOT auto-update on disk changes. Use `!CommandMeasure ParentMeasure "Update"` to force re-read.

### Parent Measure Options

| Option | Default | Notes |
|---|---|---|
| `Path` | This PC | Folder to read |
| `FinishAction` | None | Action when reading completes |
| `Recursive` | `0` | `0`=flat, `1`=recurse (count only), `2`=full recursive index |
| `Count` | `1` | Items to index per page |
| `ShowDotDot` | `1` | Include `..` (parent folder) entry |
| `ShowFolder` | `1` | Include folders |
| `ShowFile` | `1` | Include files |
| `ShowHidden` | `1` | Include hidden items |
| `ShowSystem` | `0` | Include system/protected items |
| `HideExtensions` | `0` | Strip extensions from `FileName` values |
| `Extensions` | — | Semicolon list: `jpg;png` — filter by extension |
| `SortType` | `Name` | `Name`, `Size`, `Type`, `Date` |
| `SortDateType` | `Modified` | `Modified`, `Created`, `Accessed` |
| `SortAscending` | `1` | `0` = descending |
| `WildcardSearch` | `*` | Wildcard filter |

### Child Measure Options

| Option | Default | Notes |
|---|---|---|
| `Path` | — | Parent measure name: `Path=[MeasureParent]` |
| `Index` | `1` | Item index (wraps at Count) |
| `IgnoreCount` | `0` | If `1`, Index = absolute position |
| `Type` | `FolderPath` | What to return (see table below) |
| `DateType` | `Modified` | Date to return for `FileDate` type |
| `IconPath` | — | Where to save extracted icon |
| `IconSize` | `Medium` | `Small`(16), `Medium`(32), `Large`(48), `ExtraLarge`(256) |

### Child `Type` Values

| Value | Returns |
|---|---|
| `FolderPath` | Parent folder path (with trailing `\`) |
| `FolderSize` | Parent folder size in bytes |
| `FileCount` | File count in parent folder |
| `FolderCount` | Subfolder count in parent folder |
| `FileName` | Name of indexed item |
| `FileType` | Extension of indexed file (empty for folders) |
| `FileSize` | Size in bytes (empty for folders) |
| `FileDate` | Date of indexed item |
| `FilePath` | Full path of indexed item |
| `PathToFile` | Path up to indexed item (trailing `\`) |
| `Icon` | Full path to saved icon image |

### Commands

| Measure | Command | Effect |
|---|---|---|
| Parent | `Update` | Re-read disk |
| Parent | `PageUp` / `PageDown` | Scroll through pages |
| Parent | `IndexUp` / `IndexDown` | Move by 1 item (good for scroll actions) |
| Parent | `PreviousFolder` | Navigate up one level |
| Child | `FollowPath` | If folder: navigate into it; if file: open with default app |
| Child | `Open` | Open file/folder |
| Either | `ContextMenu` | Show right-click context menu |
| Either | `Properties` | Show Properties dialog |

---

## FolderInfo Plugin

`Plugin=FolderInfo`

Counts files/folders and measures total size. Simpler than FileView — no indexing.

**Warning:** Scanning very large directories (e.g. `C:\Users\`) can cause instability.

### Options

| Option | Default | Notes |
|---|---|---|
| `Folder` | — | Path or reference to another FolderInfo measure |
| `InfoType` | — | `FileCount`, `FolderCount`, `FolderSize` |
| `RegExpFilter` | — | Regex to filter counted files |
| `IncludeSubFolders` | `0` | Include subdirectories |
| `IncludeHiddenFiles` | `0` | Include hidden files |
| `IncludeSystemFiles` | `0` | Include system files |

**Multiple measures sharing a folder:** Set the real path on the first measure, reference it by name (`Folder=[FirstMeasure]`) on subsequent ones.

```ini
[MeasureFolder]
Measure=Plugin
Plugin=FolderInfo
Folder=#SKINSPATH#
InfoType=FolderSize
IncludeSubFolders=1
UpdateDivider=10

[MeasureFileCount]
Measure=Plugin
Plugin=FolderInfo
Folder=[MeasureFolder]
InfoType=FileCount
UpdateDivider=10
```

---

## Tooltips (Meter General Option)

Creates a hover tooltip on any meter.

### Options

| Option | Default | Notes |
|---|---|---|
| `ToolTipText` | — | **Required.** Tooltip body text. Supports `%1`, `%2` for measure values, `#CRLF#` for newlines |
| `ToolTipTitle` | — | Tooltip title (required for icon) |
| `ToolTipIcon` | — | `Info`, `Warning`, `Error`, `Question`, `Shield`, or path to `.ico` |
| `ToolTipType` | `0` | `0` = normal, `1` = balloon |
| `ToolTipWidth` | `1000` | Max pixel width before text wraps |
| `ToolTipHidden` | `0` | `1` = hide tooltip |

**In `[Rainmeter]` section:** `ToolTipHidden=1` hides all tooltips skin-wide.

**String meter caveat:** Numeric formatting on `%1` / `%2` is overridden to `AutoScale=1`, `Scale=1`, `NumOfDecimals=0`, `Percentual=0`. Use `[SectionVariables]` with `DynamicVariables=1` for custom formatting.

```ini
[MeterCPU]
Meter=String
MeasureName=MeasureCPU
Text=CPU: %1%
ToolTipTitle=CPU Information
ToolTipType=1
ToolTipIcon=Info
ToolTipText=[MeasureCPUName]#CRLF#Running at: [MeasureCPUSpeed] MHz
DynamicVariables=1
```

---

## Fonts Guide

Fonts are used in String meters via `FontFace`. Supports `.ttf` (TrueType) and `.otf` (OpenType).

### Loading Fonts Without System Install

Place font files in `@Resources\Fonts\` — Rainmeter loads them automatically. No Windows installation required.

```
Skins\MySkin\@Resources\Fonts\MyFont.ttf
```

```ini
[MeterString]
Meter=String
FontFace=MyFontFamilyName
FontSize=14
FontColor=255,255,255,255
Text=Hello
```

Use the Windows font viewer (double-click .ttf) to find the exact **family name** to use in `FontFace`.

### Font Options

| Option | Notes |
|---|---|
| `FontFace` | Family name |
| `FontSize` | Points |
| `FontColor` | `rrr,ggg,bbb,aaa` or `rrggbbaa` hex |
| `FontWeight` | `0`–`999` (400 = normal, 700 = bold) |

### InlineSetting for Advanced Typography

```ini
[MeterString]
Meter=String
FontFace=Fira Sans
FontSize=16
FontColor=255,255,255,255
InlineSetting=Oblique
InlineSetting2=GradientColor | 180 | 255,0,0,255 ; 0.0 | 0,255,0,255 ; 0.5 | 0,0,255,255 ; 1.0
Text=Gradient Italic Text
```

**Free font sources:**
- Google Fonts: https://fonts.google.com/
- DaFont: https://www.dafont.com/
- Font Squirrel: https://www.fontsquirrel.com/

Check font licenses before redistributing with skins.

---

## ColorMatrix Guide

The `ColorMatrix` option manipulates image colors in Image meters using a 5×5 matrix. Five rows, one per `ColorMatrix1`–`ColorMatrix5`, semicolon-delimited values.

**Default (identity) matrix:**
```ini
ColorMatrix1=1; 0; 0; 0; 0   ; Red channel
ColorMatrix2=0; 1; 0; 0; 0   ; Green channel
ColorMatrix3=0; 0; 1; 0; 0   ; Blue channel
ColorMatrix4=0; 0; 0; 1; 0   ; Alpha channel
ColorMatrix5=0; 0; 0; 0; 1   ; Offsets (added to each channel)
```

Main diagonal: `Red, Green, Blue, Alpha`. Row 5 = constant offsets (0.0–1.0 range).

### Ready-to-Use Presets

**Grayscale:**
```ini
ColorMatrix1=0.33;0.33;0.33;0;0
ColorMatrix2=0.59;0.59;0.59;0;0
ColorMatrix3=0.11;0.11;0.11;0;0
ColorMatrix4=0;0;0;1;0
ColorMatrix5=0;0;0;0;1
```

**Invert colors:**
```ini
ColorMatrix1=-1;0;0;0;0
ColorMatrix2=0;-1;0;0;0
ColorMatrix3=0;0;-1;0;0
ColorMatrix4=0;0;0;1;0
ColorMatrix5=1;1;1;0;1
```

**Sepia:**
```ini
ColorMatrix1=0.393;0.349;0.272;0;0
ColorMatrix2=0.769;0.686;0.534;0;0
ColorMatrix3=0.189;0.168;0.131;0;0
ColorMatrix4=0;0;0;1;0
ColorMatrix5=0;0;0;0;1
```

**White to Alpha (make white pixels transparent):**
```ini
ColorMatrix1=1;0;0;-1;0
ColorMatrix2=0;1;0;-1;0
ColorMatrix3=0;0;1;-1;0
ColorMatrix4=0;0;0;1;0
ColorMatrix5=0;0;0;0;1
```

**Swap RGB → BGR:**
```ini
ColorMatrix1=0;0;1;0;0
ColorMatrix2=0;1;0;0;0
ColorMatrix3=1;0;0;0;0
ColorMatrix4=0;0;0;1;0
ColorMatrix5=0;0;0;0;1
```

**Black & White (high contrast):**
```ini
ColorMatrix1=1.5;1.5;1.5;0;0
ColorMatrix2=1.5;1.5;1.5;0;0
ColorMatrix3=1.5;1.5;1.5;0;0
ColorMatrix4=0;0;0;1;0
ColorMatrix5=-1;-1;-1;0;1
```

**Polaroid:**
```ini
ColorMatrix1=1.438;-0.062;-0.062;0;0
ColorMatrix2=-0.122;1.378;-0.122;0;0
ColorMatrix3=-0.016;-0.016;1.483;0;0
ColorMatrix4=0;0;0;1;0
ColorMatrix5=-0.03;0.05;-0.02;0;1
```

### Dynamic Brightness/Contrast/Saturation

```ini
[Variables]
Brightness=0
Contrast=1
Saturation=1

; c = contrast, s = saturation, b = brightness
; lumR=0.3086, lumG=0.6094, lumB=0.0820

[Image]
Meter=Image
ImageName=photo.jpg
ColorMatrix1=(#Contrast#*(((1-#Saturation#)*0.3086)+#Saturation#));(#Contrast#*((1-#Saturation#)*0.3086));(#Contrast#*((1-#Saturation#)*0.3086));0;0
ColorMatrix2=(#Contrast#*((1-#Saturation#)*0.6094));(#Contrast#*(((1-#Saturation#)*0.6094)+#Saturation#));(#Contrast#*((1-#Saturation#)*0.6094));0;0
ColorMatrix3=(#Contrast#*((1-#Saturation#)*0.0820));(#Contrast#*((1-#Saturation#)*0.0820));(#Contrast#*(((1-#Saturation#)*0.0820)+#Saturation#));0;0
ColorMatrix4=0;0;0;1;0
ColorMatrix5=(((1.0-#Contrast#)/2)+#Brightness#);(((1.0-#Contrast#)/2)+#Brightness#);(((1.0-#Contrast#)/2)+#Brightness#);0;1
DynamicVariables=1
```

---

## WebParser Tutorial

`Measure=WebParser` (or `Measure=Plugin` + `Plugin=WebParser`)

Connects to a URL, fetches raw HTML/text, and extracts data with regular expressions.

### Architecture: Parent + Children

```ini
[MeasureParent]
Measure=WebParser
URL=https://example.com/page
RegExp=(?siU)<title>(.*)</title>.*<h1>(.*)</h1>
UpdateRate=3600

[MeasureTitle]
Measure=WebParser
URL=[MeasureParent]
StringIndex=1

[MeasureHeading]
Measure=WebParser
URL=[MeasureParent]
StringIndex=2
```

Each `(.*)` capture in `RegExp` creates a numbered `StringIndex`.  
`UpdateRate` multiplied by `Update` (ms) = actual fetch interval.

### RegExp Modifiers `(?siU)`

| Flag | Effect |
|---|---|
| `s` | Dot matches newlines/tabs |
| `i` | Case-insensitive |
| `U` | Ungreedy — returns first match only |

### Debugging

Add `Debug=2` to the parent measure → downloads raw HTML to `WebParserDump.txt` in the skin folder. Remove when done.

### Downloading Images

```ini
[MeasureParent]
Measure=WebParser
URL=https://example.com/feed
RegExp=(?siU)<img src="(.+?)"

[MeasureImage]
Measure=WebParser
URL=[MeasureParent]
StringIndex=1
Download=1
; Measure value becomes the local path to the downloaded file

[MeterImage]
Meter=Image
MeasureName=MeasureImage
```

For relative URLs on the server, prepend the base: `URL=https://example.com[MeasureParent]`

### Local Files

```ini
[MeasureLocal]
Measure=WebParser
URL=file://#CURRENTPATH#data.txt
RegExp=(?siU)(.*)
```

### Lookahead Assertions for Variable-Count Items

Standard RegExp fails if it expects N items but fewer exist. Use lookahead conditionals:

```ini
; Pattern: (?(?=.*<Item>).*<Name>(.*)</Name>)
; = "If <Item> appears ahead, capture <Name>; otherwise skip"

[Variables]
Get=(?(?=.*<Item>).*<Name>(.*)</Name>)

[MeasureParent]
Measure=WebParser
URL=file://#CURRENTPATH#data.html
RegExp=(?siU)#Get##Get##Get#

[MeasureChild1]
Measure=WebParser
URL=[MeasureParent]
StringIndex=1
Substitute="":"No item"
```

This handles 0–3 items without the RegExp failing.

---

## @Include Guide

`@include` inserts another INI file's contents at the call site. Used for stylesheets, shared variables, page swapping.

### Rules

- Must appear inside a section (typically `[Rainmeter]` or `[Variables]`)
- Included file must be valid INI format
- Multiple includes need unique keys: `@include`, `@include2`, `@includeAnything`
- Use `.inc` extension to prevent the file appearing in Rainmeter's skin list
- Variables can define the include path, but that variable cannot be dynamic

```ini
[Variables]
Theme=Dark
@include=#Theme#/styles.inc
@include2=#@#shared-vars.inc
```

### How Inclusion Works

Options in the included file that already exist in the main file are **not** overwritten. New sections from the included file are inserted after the calling section.

### Practical: Stylesheet Pattern

**`@Resources/MeterStyles.inc`:**
```ini
[StyleTitle]
StringAlign=CENTER
StringCase=UPPER
FontColor=255,255,255,205
FontFace=Trebuchet MS
FontSize=10
AntiAlias=1
StringStyle=BOLD

[StyleText]
StringAlign=LEFT
FontColor=255,255,255,205
FontFace=Trebuchet MS
FontSize=8
AntiAlias=1
```

**`skin.ini`:**
```ini
[Rainmeter]
@include=#@#MeterStyles.inc

[MeterHeader]
Meter=String
MeterStyle=StyleTitle
Text=CPU
```

### Practical: Page Swapping

```ini
[Variables]
Page=1
@include=Pages\Page#Page#.inc

; To switch pages:
; [!WriteKeyValue Variables Page 2][!Refresh]
```

---

## @Resources Folder

The `@Resources` folder at the root config level is the standard location for shared assets.

**Path:** `Skins\RootConfigName\@Resources\`  
**Variable:** `#@#` expands to the @Resources folder path.

**Always use `#@#` for @Resources references** — do NOT use relative paths like `@Resources\file.png` as these break when the .ini is not in the root config folder.

### Subfolders

| Folder | Purpose | Auto-loaded? |
|---|---|---|
| `@Resources\Fonts\` | `.ttf` / `.otf` fonts | Yes — fonts available in all child skins |
| `@Resources\Cursors\` | `.ani` / `.cur` custom cursors | Yes — use via `MouseActionCursorName` |
| `@Resources\Images\` | Image files | No — reference with `#@#Images\file.png` |
| `@Resources\Scripts\` | Lua scripts | No — reference with `#@#Scripts\name.lua` |

```ini
; Good
ImageName=#@#Images\logo.png
ScriptFile=#@#Scripts\controller.lua
@include=#@#Styles.inc
LeftMouseUpAction=["#@#Addons\Tool.exe" "arg"]

; Bad (will break if .ini is in a subdirectory)
ImageName=@Resources\Images\logo.png
```

---

## Tips Index — Key Topics

Full tips catalog at https://docs.rainmeter.net/tips/

### High-Value Tips

| Tip | URL | Summary |
|---|---|---|
| !SetOption Guide | `/tips/setoption-guide/` | Dynamically change any meter/measure option |
| Fonts Guide | `/tips/fonts-guide/` | Loading and using custom fonts |
| Update Guide | `/tips/update-guide/` | Understanding update cycles |
| ColorMatrix Guide | `/tips/colormatrix-guide/` | Image color manipulation matrix |
| Counters Guide | `/tips/counters-guide/` | Building persistent numeric counters |
| Dynamic Cheat Sheet | `/tips/dynamiccheatsheet/` | Quick reference for dynamic values |
| @include Guide | `/tips/include-guide/` | File inclusion and stylesheets |
| Screen Position Variables | `/tips/screen-position-variables/` | Multi-monitor positioning |
| Transformation Matrix | `/tips/transformation-matrix-guide/` | Rotate/scale/skew meters |
| Update with Bangs | `/tips/update-with-bangs/` | Triggering updates selectively |
| Using a Measure as a Variable | `/tips/measure-as-a-variable/` | Section variables in option values |
| Wrapping Text | `/tips/wrapping-text/` | ClipString and multi-line text |

### WebParser Tips

| Tip | URL |
|---|---|
| WebParser Tutorial | `/tips/webparser-tutorial/` |
| How UpdateRate Works | `/tips/webparser-updaterate/` |
| Debugging RegExp | `/tips/webparser-debugging-regexp/` |
| RSS/Atom Feed Tutorial | `/tips/rss-feed-tutorial/` |
| Lookahead Assertions | `/tips/webparser-lookahead-assertions-in-regexp/` |
| Using StringIndex2 | `/tips/webparser-using-stringindex2/` |
| Local Files | `/tips/webparser-local-files/` |
| Relative Paths | `/tips/webparser-relative-paths/` |

### Getting Things Done

| Tip | URL |
|---|---|
| Animated .GIF Files | `/tips/animated-gif-files/` |
| Control Panel Applets | `/tips/control-panel-applets/` |
| Launching Windows Special Folders | `/tips/launching-windows-special-folders/` |
| Rotate Image Around Center | `/tips/rotate-an-image-around-its-center/` |
| Using HWiNFO with Rainmeter | `/tips/hwinfo/` |
| Advanced .rmskin Options | `/tips/advanced-rmskin-options/` |

---

## Key Gotchas (Advanced)

- **`DynamicVariables=1` required for `[SectionVariables]`** in meter/measure options
- **IfCondition fires once per state transition**, not every update — use `IfConditionMode=1` if you need continuous evaluation
- **Substitute does not affect the numeric value** — formulas and IfCondition still see the original number
- **RunCommand must be triggered** — it does nothing on skin load or normal update cycles
- **FileView does not auto-refresh** — must send `!CommandMeasure ParentMeasure "Update"`
- **@include path variables cannot be dynamic** — they are resolved at load time, not update time
- **`#@#` path is the `@Resources` folder** — do not confuse with `#CURRENTPATH#` or `#ROOTCONFIGPATH#`
- **WebParser UpdateRate** = intervals × Update ms — with Update=1000 and UpdateRate=3600, fetch happens every 60 minutes
- **ColorMatrix applies only to Image meters** — no effect on Shape, Bar, or other meter types
- **FontFace must use the family name** — find it by opening the font file in Windows Font Viewer, not the file name

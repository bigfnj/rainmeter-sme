# Rainmeter SME Reference — Part 1: Core Concepts, Measures & Variables

> Compiled from <https://docs.rainmeter.net/manual/>

---

## PART 1: GETTING STARTED

### What Is Rainmeter?

Rainmeter is a free, open-source desktop customization **platform** for Windows. It runs **skins** — movable, dynamic, sometimes-interactive windows that appear over the desktop. Rainmeter does NOT modify Windows visual styles, manage windows, or replace other applications.

| Concept | Description |
|---|---|
| **Skin** | A movable window displayed over the desktop; can be simple widget or complex mini-app |
| **Measure** | Gathers information (from hardware, files, web, etc.) — invisible by itself |
| **Meter** | Creates visual elements (text, images, charts, buttons) — displays data |
| **Bang** | Special command for inter-skin and application interaction |
| **Variable** | Short configurable text that customizes skin behavior |

### File Structure

```text
C:\Users\YourName\Documents\Rainmeter\Skins\ConfigName\SkinName.ini
```

| Term | Definition |
|---|---|
| **Config name** | Path from Skins folder to skin folder (e.g. `illustro\Clock`) |
| **Root config folder** | Top-level folder directly under `Skins\` |
| **Variant** | Multiple `.ini` files in same folder; only one active at a time |
| **Suite** | Informal grouping under same root config sharing `@Resources` |

### Minimal Skin

```ini
[Rainmeter]
Update=1000

[MeterString]
Meter=String
Text=Hello, world!
```

### INI Format Rules

1. All section names within a skin must be **unique**
2. All option names within a section must be **unique**
3. Section/option names should be **alphanumeric only** — no spaces or punctuation
4. Option values must be on a **single line**
5. Quotes around string values are **not needed**

### Section Types

| Section | Purpose |
|---|---|
| `[Rainmeter]` | Options affecting the entire skin |
| `[Variables]` | Text strings usable throughout the skin |
| Measures | Retrieve/measure information |
| Meters | Display information and visual elements |
| MeterStyles | Reusable style options for multiple meters |
| `[Metadata]` | Non-functional info: name, version, license, author |

### Files and Folders

| Path | Description |
|---|---|
| `C:\...\Documents\Rainmeter\Skins` | Default Skins folder |
| `Skins\ConfigName\SkinName.ini` | Individual skin file |
| `Skins\RootConfig\@Resources\` | Shared resources for a suite |
| `@Resources\Fonts\` | Custom font files |
| `@Resources\Cursors\` | Custom cursor files |
| `@Resources\Images\` | Shared images |

### Update Cycle

- Default: **1000 ms** (`Update` in `[Rainmeter]`)
- Each cycle: measures update → meters update → skin redraws
- `UpdateDivider` on individual meters/measures skips cycles
- `!Update` bang forces immediate update

---

## PART 2: [Rainmeter] SECTION

> **Important:** `[Rainmeter]` does NOT support Dynamic Variables or `!SetOption`, EXCEPT for `ContextTitleN` and `ContextActionN`.

### General Options

| Option | Default | Type | Description |
|---|---|---|---|
| `Update` | `1000` | Integer (ms) | Skin update interval; `-1` = update once only on load |
| `DefaultUpdateDivider` | `1` | Integer | Multiplier applied to Update for ALL measures/meters |
| `AccurateText` | `0` | Boolean | Enable Direct2D-like text rendering |
| `DynamicWindowSize` | `0` | Boolean | Auto-resize skin window to fit meters each update |
| `SkinWidth` | None | Integer (px) | Fixed skin width; truncates meters beyond boundary |
| `SkinHeight` | None | Integer (px) | Fixed skin height; truncates meters beyond boundary |
| `DragMargins` | `0,0,0,0` | left,top,right,bottom | Non-draggable margin area |
| `TransitionUpdate` | `100` | Integer (ms) | Update rate during active meter transitions |
| `ToolTipHidden` | `0` | Boolean | Hide all tooltips in the skin |
| `Group` | (none) | String | Group membership for bang targeting |
| `SelectedColor` | (system) | Color R,G,B,A | Overlay color when selected in Drag Group |
| `DragGroup` | (none) | String | Drag Group membership |

### Action Options

| Option | Fires When |
|---|---|
| `OnRefreshAction` | End of first update after load/refresh |
| `OnUpdateAction` | End of every update cycle |
| `OnCloseAction` | Skin unloaded or Rainmeter closed |
| `OnFocusAction` | Skin receives Windows focus |
| `OnUnfocusAction` | Skin loses Windows focus |
| `OnWakeAction` | Windows returns from sleep/hibernate |

### Background Options

| Option | Default | Description |
|---|---|---|
| `Background` | (none) | Path to background image file |
| `BackgroundMode` | `1` | `0`=use image, `1`=transparent, `2`=solid color, `3`=scale image, `4`=tile image |
| `BackgroundMargins` | `0,0,0,0` | Non-scaled margins for BackgroundMode=3 (9-slice) |
| `SolidColor` | `128,128,128,255` | Background fill color (BackgroundMode=2) |
| `SolidColor2` | (none) | Second gradient color |
| `GradientAngle` | (none) | Degrees — gradient direction |
| `BevelType` | `0` | `0`=none, `1`=raised, `2`=sunken |
| `BevelColor` | (none) | Primary bevel color |
| `BevelColor2` | (none) | Secondary bevel color |

> **Tip:** `SolidColor=0,0,0,1` — nearly transparent but makes background clickable.

### Context Menu Options

| Option | Max | Description |
|---|---|---|
| `ContextTitleN` | 25 | Label for custom context menu item (use `-` for separator) |
| `ContextActionN` | 25 | Action for context menu item N |

### [Metadata] Section

| Option | Description |
|---|---|
| `Name` | Display name of the skin |
| `Author` | Creator's name |
| `Information` | Description; use `\|` for line breaks |
| `Version` | Version number |
| `License` | License name |

---

## PART 3: VARIABLES

### [Variables] Section

```ini
[Variables]
MyVar=This is a string!
Red=255
url=https://www.#site#.net/
```

### Variable Syntax

| Syntax | Purpose |
|---|---|
| `#VarName#` | Normal variable reference |
| `[&VarName]` | Nested variable syntax |
| `#*Name*#` | Escaped — literal `#Name#` (not resolved) |
| `[*Name*]` | Escaped — literal `[Name]` (not resolved) |
| `%VAR_NAME%` | Windows environment variable |

### Changing Variables

| Bang | Behavior |
|---|---|
| `!SetVariable "MyVar" "NewValue"` | Changes value dynamically in memory |
| `!WriteKeyValue "Variables" "MyVar" "Value"` | Writes to INI file permanently |

### Dynamic Variables

- Set `DynamicVariables=1` on a measure or meter to re-evaluate each update
- Section variables (`[MeasureName]`) in bangs are always dynamic
- `[Rainmeter]`, `[Variables]`, `[Metadata]` do NOT support dynamic variables

### Event Variables (action context only)

| Variable | Description |
|---|---|
| `$UserInput$` | String typed into InputText plugin |
| `$MouseX$` / `$MouseY$` | Pixel position relative to meter/skin |
| `$MouseX:%$` / `$MouseY:%$` | Percent position relative to meter/skin |

---

## PART 4: BUILT-IN VARIABLES

> All path variables include a trailing backslash `\`.

### Path Variables

| Variable | Description |
|---|---|
| `#PROGRAMDRIVE#` | Drive Rainmeter is on (e.g. `C:`) |
| `#PROGRAMPATH#` | Rainmeter.exe folder |
| `#SETTINGSPATH#` | Settings files folder |
| `#SKINSPATH#` | Skins folder |
| `#PLUGINSPATH#` | Built-in plugins folder |
| `#ADDONSPATH#` | Addons folder |

### Skin Variables

| Variable | Description |
|---|---|
| `#@#` | `@Resources` folder path |
| `#CURRENTPATH#` | Folder containing current skin file |
| `#CURRENTFILE#` | Current skin filename |
| `#ROOTCONFIGPATH#` | Root config folder path |
| `#ROOTCONFIG#` | Root config name |
| `#CURRENTCONFIG#` | Full config name (e.g. `illustro\Clock`) |
| `#CURRENTCONFIGX#` | Current X position (**Dynamic**) |
| `#CURRENTCONFIGY#` | Current Y position (**Dynamic**) |
| `#CURRENTCONFIGWIDTH#` | Current skin width (**Dynamic**) |
| `#CURRENTCONFIGHEIGHT#` | Current skin height (**Dynamic**) |
| `#CURRENTCONFIGZPOS#` | Z-position (**Dynamic**) |
| `#CRLF#` | Newline character |
| `#CURRENTSECTION#` | Name of current section |
| `#CONFIGEDITOR#` | Path to configured text editor (**Dynamic**) |

### Monitor Variables (all Dynamic — need `DynamicVariables=1`)

| Variable | Description |
|---|---|
| `#WORKAREAX/Y/WIDTH/HEIGHT#` | Work area of current monitor |
| `#SCREENAREAX/Y/WIDTH/HEIGHT#` | Full screen area of current monitor |
| `#PWORKAREAX/Y/WIDTH/HEIGHT#` | Work area of primary monitor |
| `#PSCREENAREAX/Y/WIDTH/HEIGHT#` | Screen area of primary monitor |
| `#WORKAREAX@N#` etc. | Work/Screen area of monitor N |
| `#VSCREENAREAX/Y/WIDTH/HEIGHT#` | Virtual screen (all monitors combined) |

> **Work area** = usable area excluding taskbar. **Screen area** = full pixel dimensions.

---

## PART 5: SECTION VARIABLES

Reference measures and meters as variables using `[SectionName]`. Always dynamic — require `DynamicVariables=1`.

### Meter Parameters

| Parameter | Description |
|---|---|
| `[MeterName:X]` | Current X position |
| `[MeterName:Y]` | Current Y position |
| `[MeterName:W]` | Current width |
| `[MeterName:H]` | Current height |
| `[MeterName:XW]` | X + W (right edge) |
| `[MeterName:YH]` | Y + H (bottom edge) |

### Measure Parameters

| Parameter | Description |
|---|---|
| `[MeasureName]` | String value |
| `[MeasureName:]` | Number value (up to 10 decimals) |
| `[MeasureName:4]` | Number value with 4 decimal places |
| `[MeasureName:/1024]` | Number divided by 1024 |
| `[MeasureName:%]` | Number as percentage |
| `[MeasureName:MinValue]` | MinValue of measure |
| `[MeasureName:MaxValue]` | MaxValue of measure |
| `[MeasureName:EscapeRegExp]` | String with regex chars escaped |
| `[MeasureName:EncodeURL]` | URL-encoded string |
| `[MeasureName:Timestamp]` | Time measure — Windows timestamp |

Parameters can be combined: `[MeasureName:/1024,4,%]`

---

## PART 6: GENERAL MEASURE OPTIONS

Options valid for all measure types:

| Option | Default | Description |
|---|---|---|
| `Measure` | (required) | Type of measure. Cannot be changed dynamically. |
| `UpdateDivider` | `1` | Frequency multiplier. `-1` = update once only. |
| `OnUpdateAction` | (none) | Action on each measure update |
| `OnChangeAction` | (none) | Action when value changes |
| `InvertMeasure` | `0` | Inverts the measure value |
| `MinValue` | `0.0` | Minimum value for percentage range |
| `MaxValue` | `1.0` | Maximum value for percentage range |
| `AverageSize` | `1` | Average the last N values |
| `DynamicVariables` | `0` | Enable dynamic variables on this measure |
| `Disabled` | `0` | Never updates; returns `0` numerically |
| `Paused` | `0` | Never updates; freezes at last known state |
| `Group` | (none) | Group membership |

**Disabled vs. Paused:**

- `Disabled` → 0 numerically, may return old string
- `Paused` → freezes at last known number AND string

**Substitute / RegExpSubstitute:**

```ini
Substitute="find":"replace","find2":"replace2"
RegExpSubstitute=1   ; treat find patterns as PCRE regex
```

---

## PART 7: ALL MEASURE TYPES

### Calc

```ini
Measure=Calc
Formula=MeasureOne + 50
LowBound=0
HighBound=100
UpdateRandom=0      ; 1 = re-roll Random each update
UniqueRandom=0      ; 1 = no repeat until all values used
```

Special functions in Formula: `Random`, `Counter`
Number bases: `0b` (binary), `0o` (octal), `0x` (hex)
Reference other measures in Formula without brackets — no DynamicVariables needed.

### String

```ini
Measure=String
String=Red, Green, Blue
RegExpSubstitute=1
Substitute="Green":"Yellow"
```

Formulas NOT evaluated inside `String=`. InvertMeasure NOT valid.

### Time

```ini
Measure=Time
Format=%H:%M:%S
TimeZone=local         ; or GMT offset e.g. -5
DaylightSavingTime=1
```

**All format codes:** `%a %A %b %B %c %C %d %D %e %F %H %I %j %m %M %n %p %r %R %S %T %u %U %V %w %W %x %X %y %Y %z %Z %%`
Add `#` to remove leading zeros: `%#d`, `%#H`, etc.
`FormatLocale=Local` for system locale output.
`TimeStamp` accepts Windows timestamp, DST codes (DSTNextStart, DSTNextEnd, DSTStart, DSTEnd), or formatted date string + `TimeStampFormat`.

### NetIn / NetOut / NetTotal

```ini
Measure=NetIn
Interface=Best    ; or NIC name/alias/index/0=all
Cumulative=0      ; 1 = cumulative total (reset with !ResetStats)
UseBits=0         ; 1 = bits/sec instead of bytes/sec
```

Value in bytes/sec. Set MinValue/MaxValue in **bits** — Rainmeter auto-converts.
Use `Update=1000` or UpdateDivider so measures update once/second.

### CPU

```ini
Measure=CPU
Processor=0    ; 0=average, 1/2/3...=specific core
```

Returns 0–100%.

### PhysicalMemory / SwapMemory / Memory

```ini
Measure=PhysicalMemory
InvertMeasure=1    ; free instead of used
Total=0            ; 1 = return total instead of used
```

Auto-range: MinValue=0, MaxValue=total size.

### FreeDiskSpace

```ini
Measure=FreeDiskSpace
Drive=C:
Total=0           ; 1 = total drive space
Label=0           ; 1 = string value = drive label
Type=0            ; 1 = string = drive type, number = type code
IgnoreRemovable=1 ; 0 = measure USB/removable drives
DiskQuota=1       ; 0 = ignore Windows user disk quotas
```

Drive types (Type=1): Error(0), Removed(1), Removable(3), Fixed(4), Network(5), CDRom(6), Ram(7)

### Registry

```ini
Measure=Registry
RegHKey=HKEY_CURRENT_USER
RegKey=Software\MyApp
RegValue=Setting
OutputType=Value         ; Value, SubKeyList, ValueList
OutputDelimiter=#CRLF#
```

Supported types: REG_SZ, REG_EXPAND_SZ, REG_MULTI_SZ, REG_DWORD, REG_QWORD, REG_BINARY

### Process

```ini
Measure=Process
ProcessName=Firefox.exe
```

Returns `1` (running) or `-1` (not running).

### Plugin

```ini
Measure=Plugin
Plugin=PluginName
```

Built-in: ActionTimer, AudioLevel, CoreTemp, FileView, FolderInfo, InputText, Ping, Power, Quote, ResMon, RunCommand, SpeedFan, UsageMonitor, Win7Audio, WindowMessage

### Loop

```ini
Measure=Loop
StartValue=1
EndValue=100
Increment=1
LoopCount=0    ; 0 = endless
```

All values must be integers. Reset: `!CommandMeasure MeasureName "Reset"`

### Uptime

```ini
Measure=Uptime
Format=%4!i!d %3!i!h %2!i!m %1!i!s
AddDaysToHours=1    ; adds days×24 to %3 if %4 not in Format
```

Format codes: `%4`=days, `%3`=hours, `%2`=minutes, `%1`=seconds
Modifiers: `!i!`=no zeros, `!02i!`=zero-padded

### Script (Lua)

```ini
Measure=Script
ScriptFile=#@#Scripts/MyScript.lua
MyCustomOption=SomeValue
```

### WebParser

```ini
[MeasureParent]
Measure=WebParser
URL=https://example.com
RegExp=(?siU)<title>(.*)</title>
UpdateRate=600      ; multiplied by Update*UpdateDivider

[MeasureChild]
Measure=WebParser
URL=[MeasureParent]
StringIndex=1
```

Options: `Download=1` (save to temp), `ErrorString`, `LogSubstringErrors`, `CodePage`, `ProxyServer`, `UserAgent`, `Header`, `Flags`
Actions: `FinishAction`, `OnConnectErrorAction`, `OnRegExpErrorAction`, `OnDownloadErrorAction`
Force re-download: `!CommandMeasure "MeasureParent" "Update"`

### SysInfo

```ini
Measure=SysInfo
SysInfoType=COMPUTER_NAME
SysInfoData=Best    ; for network: adapter name/alias/index/Best
```

Types — General: `COMPUTER_NAME`, `USER_NAME`, `USER_SID`, `USER_LOGONTIME`, `OS_PRODUCT_NAME`, `OS_VERSION`, `OS_BITS`, `PAGESIZE`, `IDLE_TIME`
Network: `HOST_NAME`, `IP_ADDRESS`, `GATEWAY_ADDRESS`, `MAC_ADDRESS`, `ADAPTER_TYPE`, `ADAPTER_STATE`, `LAN_CONNECTIVITY`, `INTERNET_CONNECTIVITY`, `DNS_SERVER` + more
Monitor: `NUM_MONITORS`, `SCREEN_WIDTH`, `SCREEN_HEIGHT`, `WORK_AREA_WIDTH`, `WORK_AREA_HEIGHT` + more
Time Zone: `TIMEZONE_ISDST`, `TIMEZONE_BIAS`, `TIMEZONE_STANDARD_NAME`, `TIMEZONE_DAYLIGHT_NAME`

### AudioLevel

```ini
[MeasureParent]
Measure=Plugin
Plugin=AudioLevel
Port=Output        ; Output or Input
FFTSize=512        ; 0 = no FFT; power of 2 typical
Bands=16           ; log-spaced frequency bands
Sensitivity=35     ; dB range

[MeasureChild]
Measure=Plugin
Plugin=AudioLevel
Parent=MeasureParent
Channel=Sum        ; L/R/C/LFE/BL/BR/SL/SR/Sum
Type=RMS           ; RMS, Peak, FFT, FFTFreq, Band, BandFreq, Format, DeviceStatus, DeviceName, DeviceID, DeviceList
```

---

## PART 8: FORMULA REFERENCE

Valid in `Formula=`, `IfCondition=`, and anywhere a formula is expected.

### Operators

`+` `-` `*` `/` `%` (mod) `**` (power) `&` (bitwise AND) `|` (bitwise OR) `^` (bitwise XOR) `~` (bitwise NOT) `<<` `>>` (bit shift)

### Comparison

`<` `>` `<=` `>=` `=` `!=`

### Logic

`&&` `||` `!`

### Ternary

`condition ? true_value : false_value`

### Math Functions

`Abs(x)` `Ceil(x)` `Floor(x)` `Round(x[,decimals])` `Sqrt(x)` `Exp(x)` `Log(x)` `Ln(x)`
`Min(x,y)` `Max(x,y)` `Clamp(x,min,max)`
`Sin(x)` `Cos(x)` `Tan(x)` `ASin(x)` `ACos(x)` `ATan(x)` `ATan2(y,x)`
`Rad(x)` `Deg(x)` (degrees↔radians)

### Special

`Random` (Calc measure), `Counter` (Calc measure)
Constants: `PI` `E`

---

## PART 9: IfCondition / IfTrue / IfFalse

Available on all measures:

```ini
IfCondition=MeasureName > 90
IfTrueAction=[!SetOption MeterBar BarColor "255,0,0"][!UpdateMeter MeterBar][!Redraw]
IfFalseAction=[!SetOption MeterBar BarColor "0,255,0"][!UpdateMeter MeterBar][!Redraw]
IfCondition2=MeasureName < 10
IfTrueAction2=...
```

Multiple conditions supported (IfConditionN up to N=20).

`IfMatch` / `IfMatchAction` / `IfNotMatchAction` — regex match on string value:

```ini
IfMatch=^\d+$
IfMatchAction=[!Log "Value is numeric"]
```

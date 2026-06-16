# Battery / Power Skin Examples

---

## win10widgets Battery (Large, Detailed)

```
REPO: tjmarkham/win10widgets
TYPE: battery (PowerPlugin — percent, time remaining, charging state)
NOTES: Group toggling shows charging vs discharging visuals.
       ACLine measure drives which group is visible.
       Lifetime displayed as HH:MM remaining.
       Pixel-accurate Windows 10 widget aesthetic.
```

```ini
[Rainmeter]
Update=5000
AccurateText=1
DynamicWindowSize=1

[Variables]
FontFace=Segoe UI
FontColor=255,255,255,255
FontColorDim=255,255,255,128
AccentColor=0,103,192,255

[MeasureBatteryPercent]
Measure=Plugin
Plugin=PowerPlugin
PowerState=Percent
OnUpdateAction=[!SetOption MeterBatteryFill W "([MeasureBatteryPercent] * 1.22)"][!UpdateMeter MeterBatteryFill][!Redraw]

[MeasureBatteryTime]
Measure=Plugin
Plugin=PowerPlugin
PowerState=Lifetime
Format=%H:%M

[MeasureACLine]
Measure=Plugin
Plugin=PowerPlugin
PowerState=ACLine
IfEqualValue=1
IfEqualAction=[!ShowMeterGroup Charging][!HideMeterGroup Discharging][!Redraw]
IfBelowValue=1
IfBelowAction=[!HideMeterGroup Charging][!ShowMeterGroup Discharging][!Redraw]

; Battery shell outline
[MeterBatteryOutline]
Meter=Image
X=10
Y=10
W=130
H=60
SolidColor=255,255,255,50
Padding=2,2,2,2

; Battery terminal nub
[MeterBatteryTerminal]
Meter=Image
X=140
Y=25
W=8
H=30
SolidColor=255,255,255,50

; Battery fill bar
[MeterBatteryFill]
Meter=Image
X=12
Y=12
W=122
H=56
SolidColor=#AccentColor#

; Percent text
[MeterPercent]
Meter=String
MeasureName=MeasureBatteryPercent
X=75
Y=40
StringAlign=CenterCenter
FontFace=#FontFace#
FontSize=18
FontColor=#FontColor#
FontWeight=700
AntiAlias=1
Text=%1%

; Time remaining (discharging)
[MeterTimeRemaining]
Meter=String
MeasureName=MeasureBatteryTime
X=75
Y=78
StringAlign=CenterCenter
FontFace=#FontFace#
FontSize=10
FontColor=#FontColorDim#
AntiAlias=1
Text=%1 remaining
Group=Discharging

; Charging label
[MeterChargingLabel]
Meter=String
X=75
Y=78
StringAlign=CenterCenter
FontFace=#FontFace#
FontSize=10
FontColor=#FontColorDim#
AntiAlias=1
Text=Charging
Group=Charging
```

---

## Dynamic Island Battery (Shape animated, expanding)

```
REPO: KrazyManJ/rainmeter-dynamic-island
TYPE: battery (Shape animated island, expand/collapse on hover)
NOTES: Shape Container clips the battery fill bar with rounded corners.
       IfCondition on ACLine toggles charging/discharging text.
       X offset animation driven by a variable that transitions on MouseOver.
       Color changes based on battery level (critical/low/normal/charging).
```

```ini
[Rainmeter]
Update=1000
AccurateText=1
DynamicWindowSize=1

[Variables]
IslandWidth=150
IslandHeight=40
IslandX=0
Expanded=0
AccentColor=255,255,255,255
CriticalColor=255,60,60,255
LowColor=255,180,0,255
ChargingColor=80,255,120,255

[MeasureBatteryPercent]
Measure=Plugin
Plugin=PowerPlugin
PowerState=Percent

[MeasureACLine]
Measure=Plugin
Plugin=PowerPlugin
PowerState=ACLine

[MeasureStateColor]
Measure=Calc
Formula=#MeasureBatteryPercent#
; Set color based on state
IfCondition=(MeasureACLine = 1)
IfTrueAction=[!SetVariable AccentColor "80,255,120,255"][!UpdateMeter *][!Redraw]
IfCondition2=(MeasureBatteryPercent < 10) && (MeasureACLine = 0)
IfTrueAction2=[!SetVariable AccentColor "255,60,60,255"][!UpdateMeter *][!Redraw]
IfCondition3=(MeasureBatteryPercent >= 10) && (MeasureBatteryPercent < 25) && (MeasureACLine = 0)
IfTrueAction3=[!SetVariable AccentColor "255,180,0,255"][!UpdateMeter *][!Redraw]
IfCondition4=(MeasureBatteryPercent >= 25) && (MeasureACLine = 0)
IfTrueAction4=[!SetVariable AccentColor "255,255,255,255"][!UpdateMeter *][!Redraw]

; Island background
[MeterIslandBG]
Meter=Shape
X=0
Y=0
Shape=RoundRectangle 0,0,#IslandWidth#,#IslandHeight#,20 | Fill Color 20,20,20,240 | StrokeWidth 0
DynamicVariables=1
MouseOverAction=[!SetVariable Expanded 1][!SetVariable IslandWidth 280][!UpdateMeter *][!Redraw]
MouseLeaveAction=[!SetVariable Expanded 0][!SetVariable IslandWidth 150][!UpdateMeter *][!Redraw]

; Battery outline in island
[MeterBattOutline]
Meter=Shape
X=10
Y=10
Shape=RoundRectangle 0,0,60,20,5 | Fill Color 0,0,0,0 | Stroke Color #AccentColor#,200 | StrokeWidth 1.5
DynamicVariables=1

; Battery fill
[MeterBattFill]
Meter=Shape
X=12
Y=12
Shape=RoundRectangle 0,0,([MeasureBatteryPercent]*0.56),16,3 | Fill Color #AccentColor# | StrokeWidth 0
DynamicVariables=1

; Percent text
[MeterBattPercent]
Meter=String
MeasureName=MeasureBatteryPercent
X=78
Y=20
StringAlign=LeftCenter
FontFace=Segoe UI
FontSize=11
FontColor=#AccentColor#
FontWeight=600
AntiAlias=1
Text=%1%
DynamicVariables=1

; Status text (expanded only)
[MeterBattStatus]
Meter=String
X=160
Y=20
StringAlign=LeftCenter
FontFace=Segoe UI
FontSize=9
FontColor=255,255,255,160
AntiAlias=1
DynamicVariables=1
Text=[MeasureACLine]=1 ? "Charging" : ([MeasureBatteryPercent]<10 ? "Critical" : "On Battery")
Hidden=1
Group=ExpandedView
```

---

## Power Plugin Reference: All States

```
PURPOSE: Comprehensive reference showing all PowerPlugin states
TYPE: battery (reference/template)
```

```ini
[Rainmeter]
Update=5000
BackgroundMode=2
SolidColor=0,0,0,200

[MeasureACLine]
Measure=Plugin
Plugin=PowerPlugin
PowerState=ACLine
; 0 = on battery, 1 = plugged in

[MeasureBatteryStatus]
Measure=Plugin
Plugin=PowerPlugin
PowerState=Status
; 0=no battery, 1=charging, 2=critical, 3=low, 4=above low

[MeasureBatteryStatus2]
Measure=Plugin
Plugin=PowerPlugin
PowerState=Status2
; Raw SYSTEM_POWER_STATUS.BatteryFlag bitmask

[MeasureBatteryPercent]
Measure=Plugin
Plugin=PowerPlugin
PowerState=Percent
; 0-100 (255 = unknown)

[MeasureBatteryLifetime]
Measure=Plugin
Plugin=PowerPlugin
PowerState=Lifetime
Format=%H:%M
; Remaining time as formatted string. -1 = unknown (plugged in / no battery)

[MeasureCPUHz]
Measure=Plugin
Plugin=PowerPlugin
PowerState=Hz
; Rated CPU frequency in Hz

[MeasureCPUMHz]
Measure=Plugin
Plugin=PowerPlugin
PowerState=MHz
; Rated CPU frequency in MHz

[MeterStatus]
Meter=String
X=5
Y=5
FontFace=Segoe UI
FontSize=9
FontColor=255,255,255,255
AntiAlias=1
DynamicVariables=1
Text=AC: [MeasureACLine]  Status: [MeasureBatteryStatus]  Percent: [MeasureBatteryPercent]%#CRLF#Time Left: [MeasureBatteryLifetime]#CRLF#CPU: [MeasureCPUMHz] MHz
```

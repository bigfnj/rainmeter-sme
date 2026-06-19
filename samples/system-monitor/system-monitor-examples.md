# System Monitor Skin Examples

---

## Illustro CPU Monitor

```text
REPO: HarleyGorillason/illustro-Extended
TYPE: system (CPU usage + top process + uptime)
NOTES: MeterStyle pattern for consistent text styling. Bar meter for per-core usage.
       Registry measure for CPU name. AdvancedCPU plugin for top process.
```

```ini
[Rainmeter]
Update=1000
Background=#@#Background.png
BackgroundMode=3
BackgroundMargins=0,34,0,14

[Variables]
fontName=Trebuchet MS
textSize=8
colorBar=235,170,0,255
colorText=255,255,255,205

[MeasureCPUName]
Measure=Registry
RegHKey=HKEY_LOCAL_MACHINE
RegKey=HARDWARE\DESCRIPTION\System\CentralProcessor\0
RegValue=ProcessorNameString
UpdateDivider=3600
RegExpSubstitute=1
Substitute=" CPU":"","  ":"","@(.*)":""

[MeasureCPUMHz]
Measure=Plugin
Plugin=Plugins\PowerPlugin.dll
PowerState=MHZ
UpdateDivider=3600

[MeasureCore1]
Measure=CPU
Processor=1

[MeasureProcesses]
Measure=Plugin
Plugin=Perfmon.dll
PerfMonObject=System
PerfMonCounter=Processes
PerfMonInstance=
PerfMonDifference=0

[MeasureTopProcess]
Measure=Plugin
Plugin=Plugins\AdvancedCPU.dll
TopProcess=2
CPUExclude=Idle

[MeasureSystemUpTime]
Measure=UpTime
Format=".%4!i! Days, .%3!i! Hours, .%2!i! Minutes"
Substitute=".1 Days,":"1 Day,",".1 Hours,":"1 Hour,",".1 Minutes":"1 Minute",".":""
UpdateDivider=60

[styleTitle]
StringAlign=CENTER
StringCase=UPPER
StringStyle=BOLD
StringEffect=SHADOW
FontEffectColor=0,0,0,50
FontColor=#colorText#
FontFace=#fontName#
FontSize=10
AntiAlias=1

[styleLeftText]
StringAlign=LEFT
StringStyle=BOLD
StringEffect=SHADOW
FontEffectColor=0,0,0,20
FontColor=#colorText#
FontFace=#fontName#
FontSize=#textSize#
AntiAlias=1

[styleRightText]
StringAlign=RIGHT
StringStyle=BOLD
StringEffect=SHADOW
FontEffectColor=0,0,0,20
FontColor=#colorText#
FontFace=#fontName#
FontSize=#textSize#
AntiAlias=1

[styleBar]
BarColor=#colorBar#
BarOrientation=HORIZONTAL
SolidColor=255,255,255,15

[meterTitle]
Meter=STRING
MeterStyle=styleTitle
X=100
Y=12
W=190
H=18
Text=CPU
LeftMouseUpAction=!Execute ["taskmgr.exe"]
ToolTipText=Open Task Manager

[meterLabelCore1]
Meter=STRING
MeterStyle=styleLeftText
X=10
Y=18r
W=190
H=14
Text=Core 1

[meterValueCore1]
Meter=STRING
MeterStyle=styleRightText
MeasureName=MeasureCore1
X=200
Y=0r
W=190
H=14
Text=%1%

[meterBarCore1]
Meter=BAR
MeterStyle=styleBar
MeasureName=MeasureCore1
X=10
Y=12r
W=190
H=1

[meterLabelProcesses]
Meter=STRING
MeasureName=MeasureProcesses
MeterStyle=styleLeftText
X=10
Y=8r
W=190
H=14
Text=Processes: %1

[meterLabelTopProcess]
Meter=STRING
MeasureName=MeasureTopProcess
MeterStyle=styleLeftText
X=10
Y=20r
W=190
H=14
Text=Top Process: %1

[meterLabelSystemUpTime]
Meter=STRING
MeasureName=MeasureSystemUpTime
MeterStyle=styleLeftText
X=10
Y=20r
W=200
H=14
Text=Uptime: %1
```

---

## SysDash CPU with Lua Line Graph

```text
REPO: marcopixel/SysDash
TYPE: system (CPU with rolling history graph via Lua)
NOTES: GraphShape.lua generates SVG-like Path commands for a Shape meter.
       Script measure handles graph data, Shape meter renders it.
       Gradient fill under the curve via LinearGradient.
```

```ini
[Rainmeter]
Group=SysDash | CPU
Update=1000
AccurateText=1
BackgroundMode=2
SolidColor=0,0,0,1

[Variables]
Width=300
Margin=10
Scale=1
MainColor=0,180,255,255
FontColor=255,255,255,200

[ScriptGraph]
Measure=Script
ScriptFile=#@#scripts\GraphShape.lua
ShapeWidth=(#Width#*#Scale#)
ShapeHeight=(30*#Scale#)
InputMeasure=MeasureCPU
OutputGraph=ShapeGraph

[MeasureCPU]
Measure=CPU
OnUpdateAction=[!CommandMeasure ScriptGraph "Graph()"]

[MeterCPUTitle]
Meter=String
X=(#Margin#*#Scale#)
Y=(20*#Scale#)
FontFace=Segoe UI
FontSize=10
FontColor=#FontColor#
AntiAlias=1
Text=CPU

[MeterCPUValue]
Meter=String
MeasureName=MeasureCPU
X=((#Width#+#Margin#)*#Scale#)
Y=(20*#Scale#)
StringAlign=Right
FontFace=Segoe UI
FontSize=10
FontColor=#FontColor#
AntiAlias=1
Text=%1 % / %NUMBER_OF_PROCESSORS% cores

[ShapeGraph]
Meter=Shape
X=(#Margin#*#Scale#)
Y=(68*#Scale#)
H=(30*#Scale#)
Shape=Path Graph1 | StrokeWidth (3*#Scale#) | Stroke Color #MainColor# | StrokeLineJoin Round
Shape2=Path Graph2 | StrokeWidth 0 | Fill LinearGradient Grad | StrokeLineJoin Round
Graph1=0,0|Lineto 0,0
Graph2=0,0|Lineto 0,0
Grad=270 | #MainColor#,225 ; 0 | #MainColor#,0 ; 1
Padding=0,0,0,(10*#Scale#)
```

---

## SysDash Network Graph

```text
REPO: marcopixel/SysDash
TYPE: system (network download/upload with dual Lua graphs)
NOTES: Two GraphShape.lua instances — one per direction.
       Calc measure converts raw bytes to 0-100% for graph input.
       MaxDownloadMbits variable sets the scale.
```

```ini
[Rainmeter]
Group=SysDash | Network
Update=1000
AccurateText=1
BackgroundMode=2
SolidColor=0,0,0,1

[Variables]
Width=300
Margin=10
Scale=1
MainColor=0,180,255,255
MaxDownloadMbits=100
MaxUploadMbits=20

[ScriptGraphDownload]
Measure=Script
ScriptFile=#@#scripts\GraphShape.lua
ShapeWidth=(#Width#*#Scale#)
ShapeHeight=(30*#Scale#)
InputMeasure=MeasureDownloadCalc
OutputGraph=ShapeGraphDownload

[ScriptGraphUpload]
Measure=Script
ScriptFile=#@#scripts\GraphShape.lua
ShapeWidth=(#Width#*#Scale#)
ShapeHeight=(30*#Scale#)
InputMeasure=MeasureUploadCalc
OutputGraph=ShapeGraphUpload

[MeasureDownload]
Measure=NetIn
UseBits=1
Interface=Best
DynamicVariables=1
MaxValue=(#MaxDownloadMbits#*1024*1024)

[MeasureDownloadCalc]
Measure=Calc
Formula=[MeasureDownload:%]
DynamicVariables=1
MinValue=0
MaxValue=100
OnUpdateAction=[!CommandMeasure ScriptGraphDownload "Graph()"]

[MeasureUpload]
Measure=NetOut
UseBits=1
Interface=Best
DynamicVariables=1
MaxValue=(#MaxUploadMbits#*1024*1024)

[MeasureUploadCalc]
Measure=Calc
Formula=[MeasureUpload:%]
DynamicVariables=1
MinValue=0
MaxValue=100
OnUpdateAction=[!CommandMeasure ScriptGraphUpload "Graph()"]

[MeterDownloadTitle]
Meter=String
X=(#Margin#*#Scale#)
Y=(20*#Scale#)
FontFace=Segoe UI
FontSize=10
FontColor=255,255,255,200
AntiAlias=1
Text=Download

[MeterDownloadValue]
Meter=String
MeasureName=MeasureDownload
X=((#Width#+#Margin#)*#Scale#)
Y=(20*#Scale#)
StringAlign=Right
FontFace=Segoe UI
FontSize=10
FontColor=255,255,255,200
AntiAlias=1
Text=%1bps / #MaxDownloadMbits# Mbps
NumOfDecimals=1
AutoScale=1k

[ShapeGraphDownload]
Meter=Shape
X=(#Margin#*#Scale#)
Y=(68*#Scale#)
H=(30*#Scale#)
Shape=Path Graph1 | StrokeWidth (3*#Scale#) | Stroke Color #MainColor# | StrokeLineJoin Round
Shape2=Path Graph2 | StrokeWidth 0 | Fill LinearGradient Grad | StrokeLineJoin Round
Graph1=0,0|Lineto 0,0
Graph2=0,0|Lineto 0,0
Grad=270 | #MainColor#,225 ; 0 | #MainColor#,0 ; 1

[ShapeGraphUpload]
Meter=Shape
X=(#Margin#*#Scale#)
Y=(148*#Scale#)
H=(30*#Scale#)
Shape=Path Graph1 | StrokeWidth (3*#Scale#) | Stroke Color #MainColor# | StrokeLineJoin Round
Shape2=Path Graph2 | StrokeWidth 0 | Fill LinearGradient Grad | StrokeLineJoin Round
Graph1=0,0|Lineto 0,0
Graph2=0,0|Lineto 0,0
Grad=270 | #MainColor#,225 ; 0 | #MainColor#,0 ; 1
```

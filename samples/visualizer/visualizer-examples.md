# Visualizer Skin Examples

---

## Monstercat Visualizer (AudioLevel + Factory Lua)

```text
REPO: marcopixel/monstercat-visualizer
TYPE: visualizer (audio spectrum, bars, Lua-generated meters)
NOTES: Factory.lua dynamically creates the bar meters and their MeasureName bindings.
       AudioLevel plugin handles FFT with configurable bands/sensitivity/decay.
       Win7AudioPlugin detects default audio device changes.
       @include chains load bar meters, shadow meters, smoothed band measures.
       InvertVisualizer variable flips bar order left-to-right.
```

```ini
[Rainmeter]
Group=MonstercatVisualizer | Spectrum
Update=10
BackgroundMode=2
SolidColor=0,0,0,1

[Metadata]
Name=Monstercat Visualizer for Rainmeter
Author=marcopixel
Version=2.1.0
License=MIT License

[Variables]
@include=#@#variables.ini
BarCount=16
FFTSize=2048
FFTAttack=300
FFTDecay=500
FreqMin=20
FreqMax=20000
Sensitivity=80
AudioDeviceID=
Color=255,255,255,255

[MeasureWin7Audio]
Measure=Plugin
Plugin=Win7AudioPlugin
OnChangeAction=[!UpdateMeasure MeasureAudioDevice]
UpdateDivider=1000

[MeasureAudioDevice]
Measure=String
String=[MeasureWin7Audio]
OnChangeAction=[!UpdateMeasure MeasureAudio][!RefreshGroup "Spectrum"]
UpdateDivider=1000

[MeasureAudio]
Measure=Plugin
Plugin=AudioLevel
Port=Output
FFTSize=#FFTSize#
FFTOverlap=(#FFTSize#/2)
FFTAttack=#FFTAttack#
FFTDecay=#FFTDecay#
Bands=#BarCount#
FreqMin=#FreqMin#
FreqMax=#FreqMax#
Sensitivity=#Sensitivity#
ID=#AudioDeviceID#

[MeasureInvertVisualizer]
Measure=Calc
Formula=#InvertVisualizer#
IfEqualValue=1
IfEqualAction=[!SetOption ScriptFactoryBars Value3 "MeasureAudioSmoothed{#BarCount# - %% - 1}"][!CommandMeasure "ScriptFactoryBars" "Initialize()"]
IfBelowValue=1
IfBelowAction=[!SetOption ScriptFactoryBars Value3 "MeasureAudioSmoothed{%%}"][!CommandMeasure "ScriptFactoryBars" "Initialize()"]
UpdateDivider=-1

[ScriptFactoryBars]
Measure=Script
ScriptFile=#@#scripts\Factory.lua
IncFile=#@#include\MeterBars.inc
Number=#BarCount#
SectionName=MeterBar%%
Option0=Meter
Value0=BAR
Option1=BarColor
Value1=#*Color*#
Option2=Group
Value2=GroupBars
Option3=MeasureName
Value3=MeasureAudioSmoothed{%%}
UpdateDivider=-1

; Included files generate:
; MeasureBands.inc    — AudioLevel Band=1..N measures
; MeasureBandsSmoothed.inc — Calc smoothing per band
; MeterBars.inc       — BAR meters referencing smoothed values
; MeterShadowBars.inc — shadow/glow effect bars
```

---

## SystemFetch Visualizer (Compact spectrum bars)

```text
REPO: Meti0X7CB/SystemFetch
TYPE: visualizer (AudioLevel, FrostedGlass, @include-driven)
NOTES: Bars and band measures fully defined in included .inc files.
       Update=60 for fast animation. FrostedGlass acrylic blur background.
```

```ini
[Rainmeter]
Update=60
AccurateText=1

[Variables]
Scale=1.5
Color=255,255,255
FrostLevel=Acrylic
Theme=Dark
CornerType=Round
@include=#@#MeasureBands.inc
@include2=#@#Blocks.inc
@include3=#@#MeterBands.inc

[FrostedGlass]
Measure=Plugin
Plugin=FrostedGlass
Type=#FrostLevel#
Border=None
Corner=#CornerType#
Backdrop=#Theme#
BorderVisible=0

[MeterCanvas]
Meter=Shape
Shape=Rectangle 0,0,(315*#Scale#),(110*#Scale#),5 | Fill Color 0,0,0,1 | StrokeWidth 0
```

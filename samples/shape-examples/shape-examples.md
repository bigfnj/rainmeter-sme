# Shape-Only Skin Examples

Shape meters use vector drawing with no image file dependencies. All rendering is done via the `Shape=` option.

---

## Shape Primitives Reference

```ini
[Rainmeter]
Update=1000
DynamicWindowSize=1

; All Shape primitives with options

[MeterRectangle]
Meter=Shape
X=10
Y=10
; Basic rectangle: X,Y,Width,Height[,CornerRadius]
Shape=Rectangle 0,0,100,50 | Fill Color 0,120,215,255 | StrokeWidth 2 | Stroke Color 255,255,255,128

[MeterRoundRect]
Meter=Shape
X=120
Y=10
; Rounded rectangle
Shape=RoundRectangle 0,0,100,50,10 | Fill Color 80,80,80,200 | StrokeWidth 0

[MeterEllipse]
Meter=Shape
X=230
Y=10
; Ellipse: CenterX,CenterY,RadiusX[,RadiusY] (circle if RadiusY omitted)
Shape=Ellipse 30,25,30,25 | Fill Color 255,80,80,255 | StrokeWidth 3 | Stroke Color 255,255,255,255

[MeterLine]
Meter=Shape
X=10
Y=70
; Line: X1,Y1,X2,Y2
Shape=Line 0,0,100,0 | StrokeWidth 2 | Stroke Color 255,255,255,200

[MeterArc]
Meter=Shape
X=120
Y=70
; Arc: X,Y,Width,Height,StartAngle,SweepAngle (radians)
Shape=Arc 0,0,60,60,(PI/2),(3*PI/2) | StrokeWidth 3 | Stroke Color 0,200,100,255 | Fill Color 0,0,0,0

[MeterPath]
Meter=Shape
X=10
Y=140
; Path: StartX,StartY | Operation X,Y | ...
; Operations: Lineto, Curveto (bezier), ArcTo, ClosePath
Shape=Path 0,50 | Lineto 25,0 | Lineto 50,50 | ClosePath True | Fill Color 255,200,0,255 | StrokeWidth 0
```

---

## Shape Modifiers Reference

```ini
; Modifiers applied after the shape name with | separator

[MeterShapeModifiers]
Meter=Shape
X=10
Y=10
; Fill options
Shape=Rectangle 0,0,200,80 | Fill Color 0,120,215,255
; Fill LinearGradient: Angle | Color,Alpha ; Offset | Color,Alpha ; Offset
Shape2=Rectangle 0,90,200,80 | Fill LinearGradient 90 | 255,0,0,255 ; 0 | 0,0,255,255 ; 1
; Fill RadialGradient: CX,CY,RX,RY | Color,Alpha ; Offset | ...
Shape3=Rectangle 0,180,200,80 | Fill RadialGradient 100,40,100,40 | 255,255,255,255 ; 0 | 0,0,0,255 ; 1
; Stroke options
Shape4=Rectangle 0,270,200,80 | Fill Color 0,0,0,0 | Stroke Color 255,255,255,255 | StrokeWidth 3 | StrokeLineJoin Round | StrokeStartCap Round | StrokeEndCap Round
; Transform: Rotate, Scale, Skew, Offset
Shape5=Rectangle 0,360,100,50 | Fill Color 0,120,215,200 | Rotate 45 | Scale 1.5,1 | Offset 50,0
```

---

## Progress Ring (Roundline-style via Shape)

```text
PURPOSE: Circular progress indicator using Shape Arc + Calc formula.
         Replaces Roundline meter with pure Shape for more control.
NOTES: Angle formula: value/maxValue * 2*PI for full arc.
       DynamicVariables=1 required for formula in Shape option.
```

```ini
[Rainmeter]
Update=1000
DynamicWindowSize=1

[Variables]
CX=60
CY=60
Radius=50
StrokeW=8
BGColor=50,50,50,200
FGColor=0,180,255,255

[MeasureCPU]
Measure=CPU

[MeterProgressBG]
Meter=Shape
X=(#CX#-#Radius#-#StrokeW#)
Y=(#CY#-#Radius#-#StrokeW#)
; Full circle background track
Shape=Arc 0,0,((#Radius#+#StrokeW#)*2),((#Radius#+#StrokeW#)*2),(-(PI/2)),(2*PI) | StrokeWidth #StrokeW# | Stroke Color #BGColor# | Fill Color 0,0,0,0 | StrokeStartCap Round | StrokeEndCap Round

[MeterProgressFG]
Meter=Shape
X=(#CX#-#Radius#-#StrokeW#)
Y=(#CY#-#Radius#-#StrokeW#)
; CPU arc — sweep angle = (pct/100) * 2PI
Shape=Arc 0,0,((#Radius#+#StrokeW#)*2),((#Radius#+#StrokeW#)*2),(-(PI/2)),([MeasureCPU]/100*2*PI) | StrokeWidth #StrokeW# | Stroke Color #FGColor# | Fill Color 0,0,0,0 | StrokeStartCap Round | StrokeEndCap Round
DynamicVariables=1

[MeterLabel]
Meter=String
MeasureName=MeasureCPU
X=#CX#
Y=#CY#
StringAlign=CenterCenter
FontFace=Segoe UI
FontSize=14
FontColor=255,255,255,255
FontWeight=300
AntiAlias=1
NumOfDecimals=0
Text=%1%
```

---

## mnmlUI Analog Clock (All-Shape hands)

```text
REPO: khanhas/mnmlUI
TYPE: clock (shape-only)
NOTES: Hour and minute hands as rotated rounded rectangles.
       Vertical accent bar as design element.
       All rendering via Shape — zero image dependencies.
```

```ini
[Rainmeter]
Update=3000
AccurateText=1
DynamicWindowSize=1

[Variables]
Color=255,255,255
Scale=1

[MeasureHour]
Measure=Time
Format=%I

[MeasureMinute]
Measure=Time
Format=%M

[MeterClock]
Meter=Shape
X=(100*#Scale#)
Y=(100*#Scale#)
; Outer ring
Shape =Ellipse 0,0,(90*#Scale#) | StrokeWidth (3*#Scale#) | StrokeColor #Color# | Fill Color 0,0,0,0
; Hour hand — rotated rounded rectangle
Shape2=Rectangle (-4*#Scale#+(45/2)*#Scale#*cos(PI/2-([MeasureHour]*60+[MeasureMinute])/720*2*PI)),((-45/2)*#Scale#-(45/2)*#Scale#*sin(PI/2-([MeasureHour]*60+[MeasureMinute])/720*2*PI)),(8*#Scale#),(45*#Scale#),(4*#Scale#) | Rotate (([MeasureHour]*60+[MeasureMinute])/720*360) | StrokeWidth 0 | Fill Color #Color#
; Minute hand — longer, rotated rounded rectangle
Shape3=Rectangle (-4*#Scale#+35*#Scale#*cos(PI/2-[MeasureMinute]/60*2*PI)),(-35*#Scale#-35*#Scale#*sin(PI/2-[MeasureMinute]/60*2*PI)),(8*#Scale#),(70*#Scale#),(4*#Scale#) | Rotate ([MeasureMinute]/60*360) | StrokeWidth 0 | Fill Color #Color#
; Center dot
Shape4=Ellipse 0,0,(10*#Scale#) | StrokeWidth 0 | Fill Color #Color#
; Vertical accent bar
Shape5=Rectangle (150*#Scale#),(-90*#Scale#),(3*#Scale#),(180*#Scale#) | StrokeWidth 0 | Fill Color #Color#
DynamicVariables=1
```

---

## SystemFetch Analog Clock (Three-hand with FrostedGlass)

```text
REPO: Meti0X7CB/SystemFetch
TYPE: clock (shape-only, acrylic)
NOTES: Second hand in accent color. FrostedGlass for Windows 11 acrylic look.
       Each hand as a Rectangle with Rotate modifier around the clock center.
       Offset modifier adjusts the pivot point.
```

```ini
[Rainmeter]
Update=1000
AccurateText=1

[Variables]
Scale=1.5
Color=240,240,240
SubColor=199,199,199
Highlight=254,105,97

[FrostedGlass]
Measure=Plugin
Plugin=FrostedGlass
Type=Acrylic
Corner=Round

[MeterCanvas]
Meter=Shape
Shape=Rectangle 0,0,(300*#Scale#),(300*#Scale#),5 | Fill Color 0,0,0,1 | StrokeWidth 0

[MeasureHour]
Measure=Time
Format=%I

[MeasureMinute]
Measure=Time
Format=%M

[MeasureSecond]
Measure=Time
Format=%S

[MeterHands]
Meter=Shape
; Hour hand (short, thick, SubColor)
Shape =Rectangle (150*#Scale#),(150*#Scale#),(10*#Scale#),(-50*#Scale#),(5*#Scale#) | Offset (-12/2*#Scale#),(-40*#Scale#) | Rotate (([MeasureHour]*60+[MeasureMinute])/720*360),(12/2*#Scale#),(90*#Scale#) | StrokeWidth 0 | Fill Color #SubColor#
; Minute hand (medium, Color)
Shape2=Rectangle (150*#Scale#),(150*#Scale#),(8*#Scale#),(-75*#Scale#),(4*#Scale#) | Offset (-8/2*#Scale#),(-30*#Scale#) | Rotate ([MeasureMinute]/60*360),(8/2*#Scale#),(105*#Scale#) | StrokeWidth 0 | Fill Color #Color#
; Second hand (thin, Highlight/accent)
Shape3=Rectangle (150*#Scale#),(150*#Scale#),(4*#Scale#),(-100*#Scale#),(2*#Scale#) | Offset (-4/2*#Scale#),(-20*#Scale#) | Rotate ([MeasureSecond]/60*360),(4/2*#Scale#),(120*#Scale#) | StrokeWidth 0 | Fill Color #Highlight#
DynamicVariables=1
AntiAlias=1

[MeterCenter]
Meter=Shape
Shape=Ellipse (150*#Scale#),(150*#Scale#),(7*#Scale#) | Fill Color #SubColor# | StrokeWidth 0
```

---

## Dynamic Island Shape (Battery Expanding Animation)

```text
REPO: KrazyManJ/rainmeter-dynamic-island
TYPE: shape-only UI element
NOTES: MouseOver expands IslandWidth via !SetVariable.
       Shape RoundRectangle with DynamicVariables redraws at new width.
       Battery fill bar width driven by battery percentage formula.
       All UI elements drawn with Shape — no images.
```

```ini
[Rainmeter]
Update=1000
DynamicWindowSize=1

[Variables]
IslandWidth=150
IslandHeight=40
AccentColor=255,255,255,255

[MeasureBatteryPercent]
Measure=Plugin
Plugin=PowerPlugin
PowerState=Percent

[MeterIslandBG]
Meter=Shape
X=0
Y=0
Shape=RoundRectangle 0,0,#IslandWidth#,#IslandHeight#,20 | Fill Color 20,20,20,240 | StrokeWidth 0
DynamicVariables=1
MouseOverAction=[!SetVariable IslandWidth 280][!UpdateMeter *][!Redraw]
MouseLeaveAction=[!SetVariable IslandWidth 150][!UpdateMeter *][!Redraw]

[MeterBattOutline]
Meter=Shape
X=10
Y=10
Shape=RoundRectangle 0,0,60,20,5 | Fill Color 0,0,0,0 | Stroke Color #AccentColor#,150 | StrokeWidth 1.5

[MeterBattFill]
Meter=Shape
X=12
Y=12
; Width = 0 to 56 based on battery %
Shape=RoundRectangle 0,0,([MeasureBatteryPercent]*0.56),16,3 | Fill Color #AccentColor# | StrokeWidth 0
DynamicVariables=1

[MeterBattTerminal]
Meter=Shape
X=72
Y=17
Shape=RoundRectangle 0,0,4,6,2 | Fill Color #AccentColor#,120 | StrokeWidth 0
```

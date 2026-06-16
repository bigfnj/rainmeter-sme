# Clock Skin Examples

---

## Pacific Minimalist Clock

```
REPO: khanhas/mnmlUI
TYPE: clock (digital, large display)
NOTES: Large Pacifico font month + GeosansLight time/date. Scale variable for DPI.
```

```ini
[Rainmeter]
Update=3000
AccurateText=1
DynamicWindowSize=1

[Variables]
Color=255,255,255
Scale=1

[MeasureMonth]
Measure=Time
Format=%b.

[Month]
Meter=String
MeasureName=MeasureMonth
FontColor=#Color#
FontFace=Pacifico
FontSize=(100*#scale#)
StringAlign=Right
AntiAlias=1
Y=(-60*#scale#)
X=(500*#scale#)

[MeasureYearTimeDate]
Measure=Time
Format=%Y#CRLF#%I:%M %p on %A %d

[YearTimeDate]
Meter=String
MeasureName=MeasureYearTimeDate
FontColor=#Color#
FontFace=GeosansLight
FontSize=(22*#scale#)
StringCase=Upper
StringAlign=Right
AntiAlias=1
Y=(-30*#scale#)R
X=(-30*#scale#)r
```

---

## Illustro Clock (Official Illustro Style)

```
REPO: HarleyGorillason/illustro-Extended
TYPE: clock (digital, illustro dark panel)
NOTES: MeterStyle pattern, background PNG, uppercase styling.
```

```ini
[Rainmeter]
Author=poiru
Update=1000
Background=#@#Background.png
BackgroundMode=3
BackgroundMargins=0,34,0,14

[Variables]
fontName=Trebuchet MS
textSize=8
colorBar=235,170,0,255
colorText=255,255,255,205

[measureTime]
Measure=Time
Format=%H:%M

[measureDate]
Measure=Time
Format=%d.%m.%Y

[measureDay]
Measure=Time
Format=%A

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
ClipString=1

[styleLeftText]
StringAlign=LEFT
StringStyle=BOLD
StringEffect=SHADOW
FontEffectColor=0,0,0,20
FontColor=#colorText#
FontFace=#fontName#
FontSize=#textSize#
AntiAlias=1
ClipString=1

[styleRightText]
StringAlign=RIGHT
StringStyle=BOLD
StringEffect=SHADOW
FontEffectColor=0,0,0,20
FontColor=#colorText#
FontFace=#fontName#
FontSize=#textSize#
AntiAlias=1
ClipString=1

[meterTitle]
Meter=STRING
MeterStyle=styleTitle
MeasureName=measureTime
X=100
Y=12
W=190
H=18
Text="%1"

[meterDay]
Meter=STRING
MeterStyle=styleLeftText
MeasureName=measureDay
X=10
Y=40
W=190
H=14
Text="%1"

[meterDate]
Meter=STRING
MeterStyle=styleRightText
MeasureName=measureDate
X=200
Y=0r
W=190
H=14
Text="%1"
```

---

## SysDash Clock (Inline Font Sizing)

```
REPO: marcopixel/SysDash
TYPE: clock (digital, 24h / 12h switch via IfCondition)
NOTES: InlineSetting for font variant sizing (AM/PM smaller than time digits).
       Scale variable, dynamic locale for date formatting.
```

```ini
[Rainmeter]
Group=SysDash | Clock
Update=1000
DynamicWindowSize=1
AccurateText=1
BackgroundMode=2
SolidColor=0,0,0,1

[Variables]
@include=#@#variables.ini
Use24HourFormat=1
DateLanguage=en-US
FontClock=Segoe UI Light
FontColor=255,255,255,255
Width=300
Margin=10
Scale=1

[MeasureTime]
Measure=Time
Format=%H:%M
IfCondition=#Use24HourFormat# = 1
IfTrueAction=[!SetOption MeasureTime Format "%H:%M"][!UpdateMeasure "MeasureTime"]
IfFalseAction=[!SetOption MeasureTime Format "%#I:%M %p"][!UpdateMeasure "MeasureTime"]

[MeasureDate]
Measure=Time
Format=%A, %#d %B %Y
TimeStampLocale=#DateLanguage#
FormatLocale=#DateLanguage#

[MeterTime]
Meter=String
MeasureName=MeasureTime
X=((#Width#+(#Margin#*2))*#Scale#)/2
Y=(15*#Scale#)
W=(#Width#*#Scale#)
StringAlign=Center
InlineSetting=Face | #FontClock#
InlineSetting2=Size | (68*#Scale#)
InlineSetting3=Weight | 300
InlineSetting4=Color | #FontColor#
InlineSetting5=Size | (42*#Scale#)
InlinePattern5=(PM|AM)
AntiAlias=1
Text=%1

[MeterDate]
Meter=String
MeasureName=MeasureDate
X=((#Width#+(#Margin#*2))*#Scale#)/2
Y=(-5*#Scale#)R
W=(#Width#*#Scale#)
StringAlign=Center
StringCase=Upper
InlineSetting=Face | #FontClock#
InlineSetting2=Size | (14*#Scale#)
InlineSetting3=Weight | 300
InlineSetting4=Color | #FontColor#
AntiAlias=1
Text=%1
```

---

## Clock Future Style (Shape-Only Analog Clock)

```
REPO: khanhas/mnmlUI
TYPE: clock (shape-based analog) + digital readout
NOTES: All-Shape analog face — hour/minute hands drawn as rotated rectangles.
       DynamicVariables=1 required for the hand angle math.
       Uses trigonometry for hand positioning.
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

[Shape]
Meter=Shape
X=(100*#Scale#)
Y=(100*#Scale#)
; Outer ring
Shape = Ellipse 0,0,(90*#Scale#) | StrokeWidth (3*#Scale#) | StrokeColor #Color# | Fill Color 0,0,0,0
; Hour hand
Shape2= Rectangle (-4*#Scale#+(45/2)*#Scale#*cos(PI/2-([MeasureHour]*60+[MeasureMinute])/720*2*PI)),((-45/2)*#Scale#-(45/2)*#Scale#*sin(PI/2-([MeasureHour]*60+[MeasureMinute])/720*2*PI)),(8*#Scale#),(45*#Scale#),(4*#Scale#) | Rotate (([MeasureHour]*60+[MeasureMinute])/720*360) | StrokeWidth 0
; Minute hand
Shape3= Rectangle (-4*#Scale#+35*#Scale#*cos(PI/2-[MeasureMinute]/60*2*PI)),(-35*#Scale#-35*#Scale#*sin(PI/2-[MeasureMinute]/60*2*PI)),(8*#Scale#),(70*#Scale#),(4*#Scale#) | Rotate ([MeasureMinute]/60*360) | StrokeWidth 0
; Center dot
Shape4= Ellipse 0,0,(10*#Scale#) | StrokeWidth 0 | Fill Color #color#
; Vertical accent line
Shape5= Rectangle (150*#Scale#),(-90*#Scale#),(3*#Scale#),(180*#Scale#) | StrokeWidth 0 | Fill Color #color#
DynamicVariables=1

[MeasureTime]
Measure=Time
Format=%I:%M%p

[MeasureDate]
Measure=Time
Format=%B %d,%Y
FormatLocale=Local

[Time]
Meter=String
MeasureName=MeasureTime
FontColor=#Color#
FontFace=Futurist Fixed-width
FontSize=(31*#scale#)
StringCase=Upper
AntiAlias=1
Y=(30*#scale#)
X=(300*#scale#)

[Date]
Meter=String
MeasureName=MeasureDate
FontColor=#Color#
FontFace=Futurist Fixed-width
FontSize=(20*#scale#)
StringCase=Upper
AntiAlias=1
Y=(10*#scale#)R
X=r
```

---

## SystemFetch Analog Clock (Shape hands, FrostedGlass)

```
REPO: Meti0X7CB/SystemFetch
TYPE: clock (shape analog, Windows 11 acrylic style)
NOTES: FrostedGlass plugin for acrylic blur. All three hands (hour/minute/second)
       drawn as rotated Shape rectangles. DynamicVariables on the hands meter.
```

```ini
[Rainmeter]
Update = 1000
AccurateText = 1

[Variables]
Scale = 1.5
Color = 240,240,240
SubColor = 199,199,199
Highlight = 254,105,97
FrostLevel = Acrylic
Theme = Dark
CornerType = Round

[FrostedGlass]
Measure = Plugin
Plugin = FrostedGlass
Type = #FrostLevel#
Corner = #CornerType#
Backdrop = #Theme#

[MeterCanvas]
Meter = Shape
Shape = Rectangle 0,0,(300*#Scale#),(300*#Scale#),5 | Fill Color 0,0,0,1 | StrokeWidth 0

[MeasureHour]
Measure = Time
Format = %I

[MeasureMinute]
Measure = Time
Format = %M

[MeasureSecond]
Measure = Time
Format = %S

[MeterCenter]
Meter = Shape
Shape = Ellipse (150*#Scale#),(150*#Scale#),(7*#Scale#) | Fill Color #SubColor# | StrokeWidth 0

[MeterHands]
Meter = Shape
; Hour hand (short, thick, rounded)
Shape  = Rectangle (150*#Scale#),(150*#Scale#),(10*#Scale#),(-50*#Scale#),(5*#Scale#) | Offset (-12/2*#Scale#),(-40*#Scale#) | Rotate (([MeasureHour]*60+[MeasureMinute])/720*360),(12/2*#Scale#),(90*#Scale#) | StrokeWidth 0 | Fill Color #SubColor#
; Minute hand (medium, white)
Shape2 = Rectangle (150*#Scale#),(150*#Scale#),(8*#Scale#),(-75*#Scale#),(4*#Scale#)  | Offset (-8/2*#Scale#), (-30*#Scale#) | Rotate ([MeasureMinute]/60*360),(8/2*#Scale#),(105*#Scale#) | StrokeWidth 0 | Fill Color #Color#
; Second hand (thin, accent color)
Shape3 = Rectangle (150*#Scale#),(150*#Scale#),(4*#Scale#),(-100*#Scale#),(2*#Scale#) | Offset (-4/2*#Scale#), (-20*#Scale#) | Rotate ([MeasureSecond]/60*360),(4/2*#Scale#),(120*#Scale#) | StrokeWidth 0 | Fill Color #Highlight#
DynamicVariables = 1
AntiAlias = 1
```

---

## Retro Word Clock (Substitute for number-to-word)

```
REPO: khanhas/mnmlUI
TYPE: clock (word display using Substitute)
NOTES: Substitute converts numeric time to English words.
       InlineSetting dims certain characters (the separator).
```

```ini
[Rainmeter]
Update=3000

[Variables]
Color=255,255,255
Size=160

[MeasureTime]
Measure=Time
Format=%H %M
Substitute="00":"Zero","01":"One","02":"Two","03":"Three","04":"Four","05":"Five","06":"Six","07":"Seven","08":"Eight","09":"Nine","10":"Ten","11":"Eleven","12":"Twelve","13":"Thirteen","14":"Fourteen","15":"Fifteen","16":"Sixteen","17":"Seventeen","18":"Eighteen","19":"Nineteen","20":"Twenty","21":"TwentyOne","22":"TwentyTwo","23":"TwentyThree","24":"TwentyFour","25":"TwentyFive","26":"TwentySix","27":"TwentySeven","28":"TwentyEight","29":"TwentyNine","30":"Thirty","31":"ThirtyOne","32":"ThirtyTwo","33":"ThirtyThree","34":"ThirtyFour","35":"ThirtyFive","36":"ThirtySix","37":"ThirtySeven","38":"ThirtyEight","39":"ThirtyNine","40":"Forty","41":"FortyOne","42":"FortyTwo","43":"FortyThree","44":"FortyFour","45":"FortyFive","46":"FortySix","47":"FortySeven","48":"FortyEight","49":"FortyNine","50":"Fifty","51":"FiftyOne","52":"FiftyTwo","53":"FiftyThree","54":"FiftyFour","55":"FiftyFive","56":"FiftySix","57":"FiftySeven","58":"FiftyEight","59":"FiftyNine"

[TimeMeter]
Meter=String
MeasureName=MeasureTime
FontSize=(#size#*21/100)
FontFace=Product Sans
FontColor=#Color#
StringCase=Upper
StringAlign=CenterCenter
AntiAlias=1
X=r
Y=(#size#/1.75)r
DynamicVariables=1
```

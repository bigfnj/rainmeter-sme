# Now Playing Skin Examples

---

## Illustro foobar2000 Now Playing

```text
REPO: HarleyGorillason/illustro-Extended
TYPE: now-playing (NowPlaying plugin, CAD/foobar2000)
NOTES: MeasureName chain — first measure sets PlayerName, rest reference it.
       Album art via NowPlaying COVER type. Progress as BAR meter.
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

[MeasurePlayer]
Measure=Plugin
Plugin=NowPlaying
PlayerName=CAD
PlayerType=TITLE
DisableLeadingZero=1
Substitute="":"N/A"

[MeasureArtist]
Measure=Plugin
Plugin=NowPlaying
PlayerName=[MeasurePlayer]
PlayerType=ARTIST
Substitute="":"N/A"

[MeasureAlbum]
Measure=Plugin
Plugin=NowPlaying
PlayerName=[MeasurePlayer]
PlayerType=ALBUM
Substitute="":"N/A"

[MeasureArtwork]
Measure=Plugin
Plugin=NowPlaying.dll
PlayerName=[MeasurePlayer]
PlayerType=COVER

[MeasurePosition]
Measure=Plugin
Plugin=NowPlaying.dll
PlayerName=[MeasurePlayer]
PlayerType=POSITION

[MeasureProgress]
Measure=Plugin
Plugin=NowPlaying.dll
PlayerName=[MeasurePlayer]
PlayerType=PROGRESS

[styleTitle]
StringAlign=CENTER
StringCase=UPPER
StringStyle=BOLD
FontColor=#colorText#
FontFace=#fontName#
FontSize=10
AntiAlias=1

[styleLeftText]
StringAlign=LEFT
StringStyle=BOLD
FontColor=#colorText#
FontFace=#fontName#
FontSize=#textSize#
AntiAlias=1

[styleRightText]
StringAlign=RIGHT
StringStyle=BOLD
FontColor=#colorText#
FontFace=#fontName#
FontSize=#textSize#
AntiAlias=1

[styleBar]
BarColor=#colorBar#
BarOrientation=HORIZONTAL
SolidColor=255,255,255,15

[MeterTitle]
Meter=STRING
MeterStyle=styleTitle
X=100
Y=12
W=50
H=18
Text=Music
LeftMouseUpAction=!Execute [!CommandMeasure MeasurePlayer "OpenPlayer"]

[MeterArtwork]
Meter=IMAGE
MeasureName=MeasureArtwork
AntiAlias=1
PreserveAspectRatio=1
X=33
Y=40
H=140
W=140

[MeterTrack]
Meter=String
MeterStyle=styleLeftText
MeasureName=MeasurePlayer
X=10
Y=185
W=95
H=14

[MeterArtist]
Meter=STRING
MeterStyle=styleRightText
MeasureName=MeasureArtist
X=200
Y=0r
W=95
H=14

[MeterAlbum]
Meter=String
MeterStyle=styleLeftText
MeasureName=MeasureAlbum
X=10
Y=205
W=95
H=14

[MeterProgressTime]
Meter=STRING
MeterStyle=styleRightText
MeasureName=MeasurePosition
X=200
Y=0r
W=190
H=14

[MeterProgressBar]
Meter=BAR
MeterStyle=styleBar
MeasureName=MeasureProgress
Y=217
X=10
W=190
H=1
```

---

## SysDash Media Player (Multi-source: Spotify, GPMDP, WebNowPlaying)

```text
REPO: marcopixel/SysDash
TYPE: now-playing (auto-detects player via IfMatch)
NOTES: IfMatch on PlayerName variable switches MPMode → updates @include path.
       WriteKeyValue persists the choice. Cover art displayed 90×90.
       Shape progress bar uses DynamicVariables + formula for width.
       Play/Pause icon loaded by filename: [MeasureStateButton].png
```

```ini
[Rainmeter]
Group=SysDash | Media
Update=100
AccurateText=1
BackgroundMode=2
SolidColor=0,0,0,1

[Variables]
Width=300
Margin=10
Scale=1
MainColor=0,180,255,255
PlayerName=Spotify
MPMode=Spotify

[MeasureSetMediaPlayer]
Measure=String
String=#PlayerName#
IfMatch=Spotify
IfMatchAction=[!WriteKeyValue Variables MPMode Spotify "#@#variables.ini"][!SetVariable MPMode Spotify][!Update]
IfMatch2=GPMDP
IfMatchAction2=[!WriteKeyValue Variables MPMode GPMDP "#@#variables.ini"][!SetVariable MPMode GPMDP][!Update]
IfNotMatchAction=[!WriteKeyValue Variables MPMode NowPlaying "#@#variables.ini"][!SetVariable MPMode NowPlaying][!Update]
UpdateDivider=-1

[MeterNoCover]
Meter=Image
ImageName=#@#images\nocover.png
X=(#Margin#*#Scale#)
Y=(20*#Scale#)
W=(90*#Scale#)
H=(90*#Scale#)
ImageTint=#MainColor#
UpdateDivider=10

[MeterCover]
Meter=Image
MeasureName=MeasureCover
X=(#Margin#*#Scale#)
Y=(20*#Scale#)
W=(90*#Scale#)
H=(90*#Scale#)
UpdateDivider=10

[MeterTrack]
Meter=String
MeasureName=MeasureTrack
X=(#Margin#*#Scale#+115*#Scale#)
Y=(35*#Scale#)
W=((#Width#-135)*#Scale#)
FontFace=Segoe UI
FontSize=9
FontColor=255,255,255,200
AntiAlias=1

[MeterControlsPrev]
Meter=Image
X=(#Margin#*#Scale#)
Y=(123*#Scale#)
W=(25*#Scale#)
H=(25*#Scale#)
LeftMouseUpAction=[!CommandMeasure MeasureState "Previous"]
ImageName=#@#images\rewind.png
ImageTint=#MainColor#

[MeterControlsPlayPause]
Meter=Image
X=8R
Y=(123*#Scale#)
W=(25*#Scale#)
H=(25*#Scale#)
LeftMouseUpAction=[!CommandMeasure MeasureState "PlayPause"]
ImageName=#@#images\[MeasureStateButton].png
DynamicVariables=1
ImageTint=#MainColor#

[MeterControlsNext]
Meter=Image
X=8R
Y=(123*#Scale#)
W=(25*#Scale#)
H=(25*#Scale#)
LeftMouseUpAction=[!CommandMeasure MeasureState "Next"]
ImageName=#@#images\fast-forward.png
ImageTint=#MainColor#

[MeterProgress]
Meter=Shape
X=(#Margin#*#Scale#+115*#Scale#)
Y=(135*#Scale#)
Shape=Rectangle 0,0,(#Width#*#Scale#-115*#Scale#),(4*#Scale#),(2*#Scale#) | Fill Color #MainColor#,50 | StrokeWidth 0
Shape2=Rectangle 0,0,((#Width#*#Scale#-115*#Scale#)*([MeasureProgress]/100)),(4*#Scale#),(2*#Scale#) | Fill Color #MainColor#,245 | StrokeWidth 0
DynamicVariables=1
UpdateDivider=10
```

---

## Cleartext Now Playing (Advanced Marquee + Container)

```text
REPO: redsaph/cleartext
TYPE: now-playing (marquee scroll, multi-player, hover controls)
NOTES: Container Shape clips the scrolling title text.
       MouseOver/MouseLeave show/hide hover controls via Groups.
       InlineSetting for dynamic font styling.
       Progress via BAR meter measuring NowPlaying PROGRESS type.
       Conditional StringAlign based on text width vs container width.
```

```ini
[Rainmeter]
Update=50
MouseOverAction=[!ShowMeterGroup Hover][!UpdateMeter *][!Redraw]
MouseLeaveAction=[!HideMeterGroup Hover][!UpdateMeter *][!Redraw]
AccurateText=1
DynamicWindowSize=1

[Variables]
skinSize=400
font_texttop=Segoe UI Light
font_textinterface=Segoe UI Semibold
color_opaque=255,255,255,200
color_translucent=255,255,255,100
color_over=0,160,255,255
align_interface=Right
player_mode=Spotify
player_controller=SpotifyPlayer

[MeasurePlayer]
Measure=Plugin
Plugin=NowPlaying
PlayerName=AIMP
PlayerType=TITLE
Substitute="":"No Track"

[MeasureArtist]
Measure=Plugin
Plugin=NowPlaying
PlayerName=[MeasurePlayer]
PlayerType=ARTIST

[MeasureProgress]
Measure=Plugin
Plugin=NowPlaying
PlayerName=[MeasurePlayer]
PlayerType=PROGRESS

[MeasurePosition]
Measure=Plugin
Plugin=NowPlaying
PlayerName=[MeasurePlayer]
PlayerType=POSITION

[MeasureLength]
Measure=Plugin
Plugin=NowPlaying
PlayerName=[MeasurePlayer]
PlayerType=DURATION

[Background]
Meter=Image
X=0
Y=0
W=(#skinSize#*1.35)
H=(#skinSize#*0.3)
SolidColor=0,0,0,1
DynamicVariables=1

[TopTextContainer]
Meter=Shape
Shape=Rectangle 0,0,300,(#skinSize#*0.085) | Fill Color 0,0,0,1 | StrokeWidth 0
X=20
Y=20

[TopText1]
Meter=String
X=0
Y=0
H=(#skinSize#*0.085)
W=300
Text=[MeasurePlayer]
FontFace=#font_texttop#
FontSize=(#skinSize#*0.0625)
FontColor=#color_opaque#
StringAlign=LeftBottom
AntiAlias=1
DynamicVariables=1
Container=TopTextContainer

[Time]
Meter=String
MeasureName=MeasurePosition
MeasureName2=MeasureLength
X=20
Y=70
FontFace=Segoe UI
FontSize=9
FontColor=#color_translucent#
AntiAlias=1
Text=%1 / %2
UpdateDivider=20

[Hairline]
Meter=Bar
MeasureName=MeasureProgress
X=20
Y=80
W=300
H=2
BarColor=#color_over#
SolidColor=#color_opaque#
BarOrientation=HORIZONTAL
UpdateDivider=20

[Play]
Meter=String
MeasureName=MeasureStateButton
X=20
Y=90
FontFace=Segoe UI
FontSize=10
FontColor=#color_translucent#
AntiAlias=1
Text=%1
LeftMouseUpAction=[!CommandMeasure MeasurePlayer "PlayPause"]
Hidden=1
Group=Hover

[Previous]
Meter=String
X=5R
Y=0r
FontFace=Segoe UI
FontSize=10
FontColor=#color_translucent#
AntiAlias=1
Text=Prev
LeftMouseUpAction=[!CommandMeasure MeasurePlayer "Previous"]
Hidden=1
Group=Hover

[Next]
Meter=String
X=5R
Y=0r
FontFace=Segoe UI
FontSize=10
FontColor=#color_translucent#
AntiAlias=1
Text=Next
LeftMouseUpAction=[!CommandMeasure MeasurePlayer "Next"]
Hidden=1
Group=Hover
```

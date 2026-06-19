# Weather Skin Examples

---

## SysDash Weather (OpenWeatherMap + WebParser)

```text
REPO: marcopixel/SysDash
TYPE: weather (current conditions, OpenWeatherMap API)
NOTES: WebParser fetches XML from OWM. IfMatch on unit variable to set metric/imperial.
       Icon files in @Resources/images/weather/ named by OWM icon code.
       ImageTint applies the skin's MainColor to weather icons.
```

```ini
[Rainmeter]
Group=SysDash | Weather
Update=10000
AccurateText=1
BackgroundMode=2
SolidColor=0,0,0,1

[Variables]
Width=300
Margin=10
Scale=1
MainColor=0,180,255,255
WeatherLocation=New York,US
WeatherAppID=YOUR_API_KEY_HERE
WeatherTemperatureUnit=F
WeatherLanguage=en
WeatherUnit=imperial

[MeasureCalcTemperatureUnit]
Measure=String
String=#WeatherTemperatureUnit#
IfMatch=C
IfMatchAction=[!SetVariable WeatherUnit "metric"][!EnableMeasure MeasureWeather]
IfMatch2=F
IfMatchAction2=[!SetVariable WeatherUnit "imperial"][!EnableMeasure MeasureWeather]
UpdateDivider=-1

[MeasureWeather]
Measure=Plugin
Plugin=WebParser
URL=http://api.openweathermap.org/data/2.5/weather?q=#WeatherLocation#&APPID=#WeatherAppID#&mode=xml&units=#WeatherUnit#&lang=#WeatherLanguage#
RegExp=(?siU)<city id="(.*)" name="(.*)">.*<country>(.*)<\/country>.*<temperature value="(.*)" min="(.*)" max="(.*)" unit="(.+)">.*<weather number="(.*)" value="(.*)" icon="(.+)">
UpdateRate=100
FinishAction=[!Update]
Disabled=1
DynamicVariables=1

[MeasureCurrentCity]
Measure=Plugin
Plugin=WebParser
URL=[MeasureWeather]
StringIndex=2

[MeasureCurrentIcon]
Measure=Plugin
Plugin=WebParser
URL=[MeasureWeather]
StringIndex=10
Substitute="":"na"

[MeasureCurrentTemp]
Measure=Plugin
Plugin=WebParser
URL=[MeasureWeather]
StringIndex=4

[MeasureTempString]
Measure=Calc
Formula=[MeasureCurrentTemp]
DynamicVariables=1
Substitute="":"N/A"

[MeasureCurrentDesc]
Measure=Plugin
Plugin=WebParser
URL=[MeasureWeather]
StringIndex=9

[MeterWeatherIcon]
Meter=Image
MeasureName=MeasureCurrentIcon
Path=#@#images\weather
X=(#Margin#*#Scale#)
Y=(20*#Scale#)
W=(40*#Scale#)
H=(40*#Scale#)
ImageTint=#MainColor#

[MeterWeatherTempText]
Meter=String
MeasureName=MeasureTempString
X=(15*#Scale#)R
Y=((80/2)*#Scale#)
FontFace=Segoe UI Light
FontSize=(32*#Scale#)
FontColor=255,255,255,255
AntiAlias=1
W=((#Width#-170)*#Scale#)
ClipString=2
Postfix=[\x00B0]
NumOfDecimals=0

[MeterWeatherCityText]
Meter=String
MeasureName=MeasureCurrentCity
X=((#Width#+#Margin#)*#Scale#)
Y=(30*#Scale#)
StringAlign=Right
FontFace=Segoe UI
FontSize=9
FontColor=255,255,255,160
AntiAlias=1

[MeterWeatherDescText]
Meter=String
MeasureName=MeasureCurrentDesc
X=0r
Y=0R
StringAlign=Right
FontFace=Segoe UI
FontSize=9
FontColor=255,255,255,160
AntiAlias=1
```

---

## Illustro Network (IP via WebParser)

```text
REPO: HarleyGorillason/illustro-Extended
TYPE: network (IP lookup + upload/download live meters)
NOTES: WebParser hits checkip.dyndns.org for external IP. NetIn/NetOut for live rates.
       Uses MaxValue variable to cap the bars (set to your connection speed in bytes/s).
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
maxDownload=16777216
MaxUpload=4096000

[measureIP]
Measure=Plugin
Plugin=WebParser.dll
Url=http://checkip.dyndns.org
UpdateRate=14400
RegExp=(?siU)Address: (.*)</body>
StringIndex=1
Substitute="":"N/A"

[measureNetIn]
Measure=NetIn
NetInSpeed=#maxDownload#

[measureNetOut]
Measure=NetOut
NetOutSpeed=#maxUpload#

[measureNetInTotal]
Measure=NetIn
Cumulative=1

[measureNetOutTotal]
Measure=NetOut
Cumulative=1

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

[meterTitle]
Meter=STRING
MeterStyle=styleTitle
X=100
Y=12
W=70
H=18
Text=Network
LeftMouseUpAction=!Execute ["::{26EE0668-A00A-44D7-9371-BEB064C98683}\3\::{8E908FC9-BECC-40F6-915B-F4CA0E70D03D}"]
ToolTipText=Open Network and Sharing Center

[meterIPLabel]
Meter=STRING
MeterStyle=styleLeftText
X=10
Y=40
W=190
H=14
Text=IP Address

[meterIPValue]
Meter=STRING
MeterStyle=styleRightText
MeasureName=measureIP
X=200
Y=0r
W=190
H=14
Text=%1

[meterUploadLabel]
Meter=STRING
MeterStyle=styleLeftText
X=10
Y=60
W=190
H=14
Text=Upload

[meterUploadValue]
Meter=STRING
MeterStyle=styleRightText
MeasureName=measureNetOut
X=200
Y=0r
W=190
H=14
Text=%1B/s
NumOfDecimals=1
AutoScale=1

[meterUploadBar]
Meter=BAR
MeterStyle=styleBar
MeasureName=measureNetOut
X=10
Y=72
W=190
H=1

[meterDownloadLabel]
Meter=STRING
MeterStyle=styleLeftText
X=10
Y=80
W=190
H=14
Text=Download

[meterDownloadValue]
Meter=STRING
MeterStyle=styleRightText
MeasureName=measureNetIn
X=200
Y=0r
W=190
H=14
Text=%1B/s
NumOfDecimals=1
AutoScale=1
```

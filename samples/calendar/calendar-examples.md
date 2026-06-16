# Calendar Skin Examples

---

## SystemFetch Calendar (Lua-driven, FrostedGlass)

```
REPO: Meti0X7CB/SystemFetch
TYPE: calendar (Lua script generates day grid, FrostedGlass acrylic)
NOTES: Calendar.lua generates the day number layout as a formatted string.
       Current day highlighted with a Shape rectangle behind it.
       InlineSetting colors Sundays/Saturdays differently from weekdays.
       MeasureCurrentDayX/Y variables position the highlight shape — set by Lua.
       FrostedGlass plugin for Windows 11 acrylic background.
```

```ini
[Rainmeter]
Update=1000
AccurateText=1

[Variables]
Scale=1.5
Color=240,240,240
SubColor=170,170,170
DeColor=100,100,100
Highlight=254,105,97
FrostLevel=Acrylic
Theme=Dark
CornerType=Round
DaysText=[MeasureDaysLayout]

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
Shape=Rectangle 0,0,(300*#Scale#),(300*#Scale#),5 | Fill Color 0,0,0,1 | StrokeWidth 0

[MeasureMonthName]
Measure=Time
Format=%B

[MeasureDaysLayout]
Measure=Script
ScriptFile=#@#Calendar.lua
DynamicVariables=1

[MeterMonth]
Meter=String
Text=[MeasureMonthName]
FontSize=(17*#Scale#)
FontColor=#Highlight#
FontFace=Segoe UI
FontWeight=600
X=(25*#Scale#)
Y=(35*#Scale#)
AntiAlias=1
InlineSetting=Case | Upper
InlineSetting2=CharacterSpacing | 0.5 | 0.5
StringAlign=LeftCenter

[MeterCurrentDay]
Meter=Shape
Shape=Rectangle (21.5*#Scale#),(92.5*#Scale#),(29*#Scale#),(29*#Scale#),(15*#Scale#) | Fill Color #Highlight#,200 | StrokeWidth 0
X=#MeasureCurrentDayX#*#Scale#
Y=#MeasureCurrentDayY#*#Scale#
DynamicVariables=1

[MeterWeekdays]
Meter=String
Text=" S   M   T   W   T   F   S "
FontSize=(13*#Scale#)
FontFace=Consolas
FontColor=#Color#
FontWeight=600
X=(21.9*#Scale#)
Y=(70*#Scale#)
AntiAlias=1
InlineSetting=Color | #SubColor#
InlinePattern=S
StringAlign=LeftCenter

[MeterDays]
Meter=String
Text=#DaysText#
FontSize=(13*#Scale#)
FontFace=Consolas
FontColor=#Color#
FontWeight=600
X=(11.9*#Scale#)
Y=(88*#Scale#)
StringAlign=LeftTop
AntiAlias=1
DynamicVariables=1
InlinePattern=(^.{3}|(?<=\n).{3})
InlineSetting=Color | #SubColor#
InlinePattern6=(^.*?(?= 1))
InlineSetting6=Color | #DeColor#
```

### Calendar.lua (Lua script for day grid generation)

```lua
-- Calendar.lua — generates a formatted string of day numbers for the current month
-- Also sets #MeasureCurrentDayX# and #MeasureCurrentDayY# variables for the highlight

function Initialize()
    Generate()
end

function Update()
    Generate()
    return SKIN:GetVariable('DaysLayout', '')
end

function Generate()
    local time = os.time()
    local today = tonumber(os.date('%d', time))
    local firstDay = os.date('*t', os.time{year=os.date('%Y',time), month=os.date('%m',time), day=1})
    local startOffset = firstDay.wday - 1  -- 0=Sun, 6=Sat
    local daysInMonth = 31
    local m = tonumber(os.date('%m', time))
    local y = tonumber(os.date('%Y', time))
    -- Days in month
    local daysPerMonth = {31,28,31,30,31,30,31,31,30,31,30,31}
    if y % 4 == 0 and (y % 100 ~= 0 or y % 400 == 0) then
        daysPerMonth[2] = 29
    end
    daysInMonth = daysPerMonth[m]

    local layout = {}
    local pos = 0
    local row = {}
    -- Fill leading blanks
    for i = 1, startOffset do
        table.insert(row, '  ')
        pos = pos + 1
    end
    for day = 1, daysInMonth do
        table.insert(row, string.format('%2d', day))
        pos = pos + 1
        if pos % 7 == 0 then
            table.insert(layout, table.concat(row, ' '))
            row = {}
        end
        if day == today then
            local col = (pos - 1) % 7
            local rowIdx = math.ceil(pos / 7) - 1
            -- Set position variables for the highlight shape
            SKIN:Bang('!SetVariable', 'MeasureCurrentDayX', tostring(col * 32 + 10))
            SKIN:Bang('!SetVariable', 'MeasureCurrentDayY', tostring(rowIdx * 32 + 85))
        end
    end
    if #row > 0 then
        table.insert(layout, table.concat(row, ' '))
    end
    SKIN:Bang('!SetVariable', 'DaysLayout', table.concat(layout, '\n'))
end
```

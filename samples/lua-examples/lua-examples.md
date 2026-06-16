# Lua Script Examples

---

## GraphShape.lua — Rolling Line Graph Generator

```
ORIGIN: marcopixel/SysDash
PURPOSE: Takes a normalized 0-100 measure value each update, maintains a history
         buffer, and outputs SVG-like Path commands for a Shape meter to render
         as a line graph with gradient fill.
USAGE: ScriptFile=#@#scripts\GraphShape.lua
       Options: ShapeWidth, ShapeHeight, InputMeasure, OutputGraph
       Call: [!CommandMeasure ScriptMeasure "Graph()"]
```

```lua
-- GraphShape.lua
-- Generates Shape Path commands for a rolling line graph

local history = {}
local maxPoints = 60  -- Number of history points to keep
local w, h, inputMeasure, outputMeter

function Initialize()
    w = tonumber(SELF:GetOption('ShapeWidth', 200))
    h = tonumber(SELF:GetOption('ShapeHeight', 50))
    inputMeasure = SELF:GetOption('InputMeasure', '')
    outputMeter = SELF:GetOption('OutputGraph', '')
    for i = 1, maxPoints do
        history[i] = 0
    end
end

function Update()
    return 0
end

function Graph()
    -- Read current value from the specified measure
    local measure = SKIN:GetMeasure(inputMeasure)
    local value = measure:GetValue()
    local pct = math.max(0, math.min(100, value)) / 100

    -- Shift history and add new value
    table.remove(history, 1)
    table.insert(history, pct)

    -- Build line path (Graph1) and fill path (Graph2)
    local stepX = w / (maxPoints - 1)
    local linePoints = {}
    local fillPoints = {}

    for i, v in ipairs(history) do
        local x = (i - 1) * stepX
        local y = h - (v * h)
        if i == 1 then
            table.insert(linePoints, string.format('%.1f,%.1f', x, y))
            table.insert(fillPoints, string.format('%.1f,%.1f', x, h))
            table.insert(fillPoints, string.format('|Lineto %.1f,%.1f', x, y))
        else
            table.insert(linePoints, string.format('|Lineto %.1f,%.1f', x, y))
            table.insert(fillPoints, string.format('|Lineto %.1f,%.1f', x, y))
        end
    end

    -- Close the fill path along the bottom
    local lastX = (maxPoints - 1) * stepX
    table.insert(fillPoints, string.format('|Lineto %.1f,%.1f', lastX, h))
    table.insert(fillPoints, string.format('|Lineto 0,%.1f', h))

    local linePath = table.concat(linePoints, '')
    local fillPath = table.concat(fillPoints, '')

    -- Update the Shape meter's Path options
    SKIN:Bang('!SetOption', outputMeter, 'Graph1', linePath)
    SKIN:Bang('!SetOption', outputMeter, 'Graph2', fillPath)
    SKIN:Bang('!UpdateMeter', outputMeter)
    SKIN:Bang('!Redraw')
end
```

---

## Factory.lua — Dynamic Meter Generator

```
ORIGIN: marcopixel/monstercat-visualizer
PURPOSE: Reads an .inc file template, creates N copies of it with substituted
         values, writes output to a temp .inc file, then triggers a skin refresh.
         Used to generate N bar meters for N audio frequency bands without
         manually writing 64+ meter sections.
USAGE: Measure=Script / ScriptFile=#@#scripts\Factory.lua
       Options: IncFile, Number, SectionName (with %% = index), Option0..N, Value0..N
       Call: [!CommandMeasure FactoryMeasure "Initialize()"]
```

```lua
-- Factory.lua
-- Generates repeated meter sections from a template

local outputFile, number, sectionName
local options, values

function Initialize()
    outputFile = SKIN:GetVariable('CURRENTPATH') .. 'generated_meters.inc'
    number = tonumber(SELF:GetOption('Number', 16))
    sectionName = SELF:GetOption('SectionName', 'MeterItem%%')

    options = {}
    values = {}
    local i = 0
    while true do
        local opt = SELF:GetOption('Option' .. i, '')
        local val = SELF:GetOption('Value' .. i, '')
        if opt == '' then break end
        options[i] = opt
        values[i] = val
        i = i + 1
    end
    Generate()
end

function Update()
    return 0
end

function Generate()
    local lines = {}
    for idx = 0, number - 1 do
        local name = sectionName:gsub('%%%%', tostring(idx))
        table.insert(lines, '[' .. name .. ']')
        for i = 0, #options do
            if options[i] then
                local val = values[i]:gsub('%%%%', tostring(idx))
                val = val:gsub('{(%d+)}', function(n)
                    return tostring(idx)
                end)
                table.insert(lines, options[i] .. '=' .. val)
            end
        end
        table.insert(lines, '')
    end

    local f = io.open(outputFile, 'w')
    if f then
        f:write(table.concat(lines, '\n'))
        f:close()
    end
end
```

---

## Refresher.lua — Delayed Skin Refresh on Load

```
ORIGIN: marcopixel/SysDash
PURPOSE: Triggers a full skin refresh after a short delay on initial load.
         Used to work around Rainmeter's @include resolution order —
         ensures all included measures are initialized before the refresh.
USAGE: Measure=Script / ScriptFile=#@#scripts\Refresher.lua
```

```lua
-- Refresher.lua
-- Single-shot delayed refresh on first update

local refreshed = false

function Initialize()
    refreshed = tonumber(SKIN:GetVariable('Refreshed', '0')) == 1
end

function Update()
    if not refreshed then
        refreshed = true
        SKIN:Bang('!WriteKeyValue', 'Variables', 'Refreshed', '1', SELF:GetOption('ScriptFile'))
        SKIN:Bang('!Refresh')
    end
    return 0
end
```

---

## Calendar.lua — Month Grid Generator

```
ORIGIN: Meti0X7CB/SystemFetch
PURPOSE: Computes the calendar grid for the current month.
         Returns a formatted multi-line string of day numbers.
         Sets #MeasureCurrentDayX# and #MeasureCurrentDayY# variables
         so a Shape meter can highlight today's cell.
USAGE: Measure=Script / ScriptFile=#@#Calendar.lua
       Reference the measure value with DynamicVariables=1 in a String meter.
```

```lua
-- Calendar.lua

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
    local m = tonumber(os.date('%m', time))
    local y = tonumber(os.date('%Y', time))

    -- First day of month weekday (0=Sun..6=Sat)
    local firstDayT = os.time{year=y, month=m, day=1}
    local firstDay = os.date('*t', firstDayT)
    local startOffset = firstDay.wday - 1

    -- Days in month
    local daysPerMonth = {31,28,31,30,31,30,31,31,30,31,30,31}
    if y % 4 == 0 and (y % 100 ~= 0 or y % 400 == 0) then
        daysPerMonth[2] = 29
    end
    local daysInMonth = daysPerMonth[m]

    local layout = {}
    local pos = 0
    local row = {}

    -- Leading blanks
    for i = 1, startOffset do
        table.insert(row, '  ')
        pos = pos + 1
    end

    for day = 1, daysInMonth do
        table.insert(row, string.format('%2d', day))
        pos = pos + 1
        if day == today then
            local col = (pos - 1) % 7
            local rowIdx = math.floor((pos - 1) / 7)
            SKIN:Bang('!SetVariable', 'MeasureCurrentDayX', tostring(col * 32 + 10))
            SKIN:Bang('!SetVariable', 'MeasureCurrentDayY', tostring(rowIdx * 32 + 85))
        end
        if pos % 7 == 0 then
            table.insert(layout, table.concat(row, ' '))
            row = {}
        end
    end
    if #row > 0 then
        table.insert(layout, table.concat(row, ' '))
    end
    SKIN:Bang('!SetVariable', 'DaysLayout', table.concat(layout, '\n'))
end
```

---

## Inline Lua: Dynamic Conditional Text

```
PURPOSE: Using Lua inline expressions directly in measure options.
         Ternary-style logic without needing a separate script file.
NOTES: Inline Lua is available in Calc measure formulas and in some bang parameters.
       Full Lua scripting (Script measure) is more reliable for complex logic.
```

```ini
; Inline formula in Calc (Lua math functions available)
[MeasureFormatted]
Measure=Calc
Formula=([MeasureCPU] > 90) ? 1 : 0
; Result: 1 if CPU > 90, else 0

; Use IfCondition instead for actions
[MeasureCPU]
Measure=CPU
IfCondition=MeasureCPU > 90
IfTrueAction=[!SetOption MeterLabel FontColor "255,0,0,255"][!UpdateMeter MeterLabel][!Redraw]
IfFalseAction=[!SetOption MeterLabel FontColor "255,255,255,255"][!UpdateMeter MeterLabel][!Redraw]

; Script measure for complex logic
[MeasureScript]
Measure=Script
ScriptFile=#@#Scripts\MyScript.lua
; Script Update() return value → measure's string value
; Script Initialize() runs once at load
```

---

## Curvs Circular Launcher Controller (Lua)

```
ORIGIN: udf/Curvs
PURPOSE: Controls a circular ring launcher — handles hover/click/leave events,
         manages button state, calculates ring geometry for N buttons around a circle.
USAGE: Measure=Script / ScriptFile=#@#Curvs.lua
       Commands via !CommandMeasure: "onHover('SectionName')", "onClick('SectionName')", "onLeave('SectionName')"
```

```lua
-- Curvs.lua (simplified version)
-- Controls a circular icon launcher

local buttons = {}
local ringCount, ringStart

function Initialize()
    ringCount = tonumber(SKIN:GetVariable('RingCount', '1'))
    ringStart = tonumber(SKIN:GetVariable('RingStart', '50'))
    BuildRings()
end

function Update()
    return ''
end

function BuildRings()
    buttons = {}
    for ring = 1, ringCount do
        local count = tonumber(SKIN:GetVariable('Ring' .. ring .. '.Count', '8'))
        local size = tonumber(SKIN:GetVariable('Ring' .. ring .. '.Size', '40'))
        local radius = ringStart + (ring - 1) * (size + 10)
        for i = 0, count - 1 do
            local angle = (2 * math.pi / count) * i - math.pi / 2
            local x = math.cos(angle) * radius
            local y = math.sin(angle) * radius
            table.insert(buttons, {ring=ring, index=i, x=x, y=y, size=size})
        end
    end
end

function onHover(section)
    SKIN:Bang('!SetOption', section, 'ImageAlpha', '255')
    SKIN:Bang('!UpdateMeter', section)
    SKIN:Bang('!Redraw')
end

function onLeave(section)
    SKIN:Bang('!SetOption', section, 'ImageAlpha', '180')
    SKIN:Bang('!UpdateMeter', section)
    SKIN:Bang('!Redraw')
end

function onClick(section)
    local action = SKIN:GetVariable(section .. '.Action', '')
    if action ~= '' then
        SKIN:Bang(action)
    end
end
```

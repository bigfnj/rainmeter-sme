# rainmeter-sme

A complete Rainmeter knowledge base and skin-building toolkit, built by reading the entire [docs.rainmeter.net](https://docs.rainmeter.net) documentation corpus and collecting 50+ real-world skin examples from popular GitHub repositories.

---

## What This Is

This repo is a **prompt-ready Rainmeter SME** — feed it to Claude (or any capable LLM) and you get a skin builder that can produce working `.ini` files from a plain-English description.

> "Build me a dark system monitor with CPU, RAM, disk, and network — Shape meter backgrounds, no image dependencies."

> "Write a calendar skin with today's date highlighted and a Lua script generating the day grid."

> "Create a now-playing skin that shows album art, scrolling track title, and a shape progress bar."

The knowledge base covers everything: every measure type, every meter type, all bangs, Lua scripting, every plugin, WebParser patterns, Shape geometry, ColorMatrix, @include stylesheets, and 10 categories of real skin examples to pattern-match against.

---

## Repository Structure

```
rainmeter-sme/
├── docs/
│   ├── 01-core-concepts-measures-variables.md   # INI format, all measures, variables, formulas
│   ├── 02-meters-bangs-lua-plugins-settings.md  # All meters, bangs, Lua API, global settings
│   └── 03-plugins-tips-advanced.md              # Plugins, WebParser, ColorMatrix, @include, tips
├── samples/
│   ├── clock/             # Digital, word clock, shape-only analog
│   ├── system-monitor/    # CPU bars, Lua line graphs, network
│   ├── weather/           # OpenWeatherMap WebParser, live rates
│   ├── now-playing/       # NowPlaying plugin, multi-source, marquee scroll
│   ├── visualizer/        # AudioLevel FFT bars, Monstercat-style
│   ├── calendar/          # Lua-driven month grid with day highlighting
│   ├── battery/           # PowerPlugin, animated Shape, all-states reference
│   ├── lua-examples/      # GraphShape, Factory, Calendar, controller patterns
│   ├── shape-examples/    # Primitives, modifiers, progress ring, analog clocks
│   ├── launcher/          # Circular Lua launcher, grid template
│   └── sample-skins-raw.md  # 24+ complete .ini files from popular repos
└── raw-snapshots/         # Browser scraping artifacts (Playwright)
```

---

## Coverage

### Measures (all 17 types)
`Calc` · `String` · `Time` · `CPU` · `Memory` · `NetIn/NetOut` · `FreeDiskSpace` · `Registry` · `Process` · `Plugin` · `Loop` · `UpTime` · `Script` · `WebParser` · `SysInfo` · `AudioLevel` · `RecycleManager`

### Meters (all 9 types)
`String` · `Image` · `Bar` · `Histogram` · `Line` · `Rotator` · `Roundline` · `Button` · `Shape`

### Plugins (full reference)
`AudioLevel` · `NowPlaying` · `WebParser` · `PowerPlugin` · `RunCommand` · `FileView` · `FolderInfo` · `QuotePlugin` · `InputText` · `Win7AudioPlugin` · `FrostedGlass` · `UsageMonitor` · `Perfmon`

### Advanced Topics
- **Lua scripting** — SKIN/Measure/Meter objects, Initialize/Update lifecycle, bang execution, inline Lua
- **Shape meter** — all primitives (Rectangle, RoundRectangle, Ellipse, Arc, Line, Path), all modifiers (Fill, Stroke, Rotate, Scale, Offset, LinearGradient, RadialGradient), Container clipping
- **WebParser** — parent/child architecture, RegExp patterns, lookahead assertions for variable-count items, local files, image downloading
- **@include** — stylesheet pattern, page-swapping pattern, @Resources folder
- **ColorMatrix** — full 5×5 matrix, 8 ready-to-use presets (grayscale, sepia, invert, white-to-alpha, black & white, polaroid, swap channels, hue shift)
- **IfCondition / IfMatch** — numeric formula conditions, string matching, multi-condition chaining, IfConditionMode
- **Substitute / RegExpSubstitute** — ordered pair replacement, capture group backreferences
- **Bangs** — all ~85 bangs documented with parameters: SetOption, SetVariable, WriteKeyValue, CommandMeasure, ShowMeter/HideMeter, GroupBang, AppToggle, and more
- **MeterStyle** — stylesheet pattern, inheritance, override behavior
- **Animation** — variable-driven transitions, update-rate optimization, Loop measure counters
- **Fonts** — @Resources/Fonts auto-loading, InlineSetting for weight/oblique/gradient/shadow

---

## How to Use

### With Claude Code

Point Claude at this repo and ask for a skin:

```
Read the docs and samples in this repo, then build me a [description] skin.
```

Or reference it in your CLAUDE.md:

```
Rainmeter knowledge base: /path/to/rainmeter-sme/
When building Rainmeter skins: read the relevant doc section and the matching
sample type before generating code.
```

### Example Prompts That Work

| Prompt | What gets built |
|---|---|
| "Dark system monitor, CPU + RAM + disk + net, Shape backgrounds, no images" | Multi-section monitor using Shape panels + String meters + Bar meters |
| "Analog clock, shape-only, no image files, accent color second hand" | Shape Rectangle hands with Rotate modifier + DynamicVariables math |
| "Calendar skin, highlight today, Lua-generated grid, acrylic blur" | Script measure + FrostedGlass + Shape highlight + String day grid |
| "Now playing with album art, scrolling title, Shape progress bar" | NowPlaying plugin + Image + Container marquee + Shape progress |
| "Battery widget, expand on hover, color changes at low/critical" | PowerPlugin + IfCondition color logic + !SetVariable hover animation |
| "Audio visualizer, 32 FFT bars, gradient fill, Monstercat style" | AudioLevel plugin + Factory.lua + 32 BAR meters |
| "WebParser skin that shows current weather from OpenWeatherMap" | WebParser parent/child + XML RegExp + image icon download |
| "Launcher with 8 icons in a circle, hover highlight, click opens app" | Lua geometry for circular layout + Shape buttons + controller script |

---

## Knowledge Sources

Documentation scraped from:
- https://docs.rainmeter.net/manual/ (full manual, 54 pages)
- https://docs.rainmeter.net/tips/ (tips & tricks catalog)

Sample skins collected from (19 repos):
`khanhas/mnmlUI` · `HarleyGorillason/illustro-Extended` · `marcopixel/SysDash` · `marcopixel/monstercat-visualizer` · `redsaph/cleartext` · `Meti0X7CB/SystemFetch` · `Meti0X7CB/SquarePlayer` · `maze404/FluentDash` · `tjmarkham/win10widgets` · `Jax-Core/ModularPlayers` · `F1uctus/Rainautica` · `Droptop-Four/Droptop-Four` · `KrazyManJ/rainmeter-dynamic-island` · `GXX0T/NotWidgets` · `lezzthanthree/Needy-Streamer-Overload` · `Runixe786/MD3-Windows` · `udf/Curvs` · `modkavartini/catppuccin` · `IgorGreenIGM/rainmeter-wakatime`

---

## Version

**v1.0.0** — Initial complete knowledge base.

- 3 documentation reference files (~2,000 lines total)
- 50+ annotated sample `.ini` files across 10 skin types
- Full Lua script examples (graph generator, factory, calendar, controller patterns)
- Complete Shape meter reference with copy-paste presets
- ColorMatrix presets, WebParser patterns, @include stylesheets

# CLAUDE.md

This file provides guidance for Claude Code (claude.ai/code) when working with the Designer theme creation project for iOS widgets. Start with the workflow!


## Project Overview

Designer is an iOS app that allows users to create custom widgets using JSON-based theme files. Themes use a hierarchical UIView structure with dynamic data integration, animations, and styling, starting with a "PresetView" container representing the phone screen dimensions (`"PresetView" : {
    "type" : "UIView",
    "frame" : {
      "y" : "0",
      "userInteractionEnabled" : "NO",
      "x" : "0",
      "width" : "screen_width",
      "height" : "screen_height"
    },
    "children" : {}}`).



## Recommended Workflow or Plan

1. **Analyze Image or Prompt (if provided)**: Extract layout specifications (element count, types, positions, content types) focusing on structure, not styling.

**CRITICAL** 
2. **Make a plan**: Plan out the UI elements now you have the structure. Look for custom fonts. If there is any UILabels involved please verify the 'data' property. Make a list of all the data values then verify it exists in `data.txt` you can do this quickly by using grep or a search. Example: Search(pattern: "temperature|humidity|batterypercent|paddedhou
        r|paddeddate|shortmonth|year|shortday", path:
        "data.txt")

3. **Create Theme**: Build a JSON theme file with proper structure, styling, and dynamic data.

4. **Dont Explain**: Once theme is created just say its done, no need to explain the theme. Just say it's done.




## Key Documentation Files

- `theme.txt`: Complete theme creation guide with syntax, components, and patterns.

- `data.txt`: Comprehensive list of all available dynamic data fields (e.g., `[paddedhour]`, `[minute]`, `[temperature]`, `[batterypercent]`, `[musicTitle]`, `[calEvent1]`).

- `final.txt`: Complete system reference for AI training with detailed examples.

- `advanced.txt`: Advanced features including blur effects, animations, and conditional display.

- `fonts.txt`: Complete iOS font reference with usage guidelines.

- `theme references/`: Example themes to check against JSON files (JN07.json, JN15.json, JN33.json, Scroll.json).



## Process Details

### 2. Theme Creation

- **Purpose**: Build or modify JSON theme files for Designer widgets.

- **Input**: Image analysis report, user requirements, or existing theme.

- **Output**: JSON file saved in `AIThemes/_<ThemeName><randomnumber>.json`, with implementation explanation.
- **Requirements**:
  - **Structure**:
    - Start with immutable PresetView container (no metadata fields like author/version).
    - Use nested UIView containers for layout.
    - All UIView elements require: `backgroundColor`, `borderWidth`, `cornerRadius` (even if default values).
    - All UILabel elements require: `data` (not `text`), `fontName` (not `font`), `textAlignment` as `NSTextAlignmentLeft/Center/Right`, `backgroundColor`, `borderWidth`, `numberOfLines`.
    - Weather icons use `WeatherIcon` type with `forecast` ("current", "day1", etc.) and `iconSet` ("clima") properties.
    - Validate property names against `theme references/`.
  
  - **Dynamic Data**:
    - Use fields from `data.txt` only (e.g., `[temperature]`, `[batterypercent]`).
    - Verify field names to avoid blank or failed displays.
    - Consider text length (e.g., avoid `[textHour] [textMinute]` for small UI, as it may produce long strings like "TEN SIXTEEN").

  - **Styling**:
    - Use rgba() color format only (e.g., `rgba(255,255,255,1.0)`), never hex.
    - Use iOS system fonts from `fonts.txt` (e.g., HelveticaNeue-UltraLight, AvenirNext-Heavy).
    - Apply glassmorphism: Blur on main containers, `blurAlpha: "0"` on children.
    - Support standard properties: animation, blurIntensity, childorder, userInteraction, cornerRadius, alpha, scale, rotate.

  - **Positioning**:
    - Support responsive positioning (e.g., `FR~20` for 20px from right, `FB~50` for 50px from bottom).
    - Math expressions (e.g., `math(view_width-40)`).
    - Avoid too complex nested math expressions.
    - Use follow width system for secondary elements tracking primary edges.
    - screen_width is width of screen, screen_view width of the parent view, same for height

    - **Container-Based Layout**: For multiple elements (like app icons), create a fixed-width container centered using `math(view_width/2-containerWidth/2)`, then use simple fixed positioning within the container.

    - **Icon Spacing Formula**: Container width = `(icon_count * icon_width) + ((icon_count + 1) * desired_spacing)`. Example: 4 icons (50px) with 20px spacing = `240px = (4×50) + (5×20)`.

    - **Consistency Strategy**: Fixed containers within responsive parents often work better than making every element responsive with complex math expressions.

  - **Animations**:
    - Define animations with `actions: "animation"`.
    - Chain animations using `trigger` property.
    - Apply delays with `wait` property.
    - Support reversible interactions with `toggle:yes/no`.

  - **Advanced Features**:
    - Use `dataAttr` for mixed label styling.
    - Implement conditional display (e.g., `showWhenPlaying`, `showWhenCharging`, `triggerOnUnlock`, `triggerOnWake`).

    - **Style Override System**: For themes with multiple AppIcon elements, implement a master style controller:
      - Create one invisible master AppIcon with `styleOverride: "none"` and desired styling properties
      - Set all other AppIcon elements to `styleOverride: "MasterStyleName"`
      - This allows bulk styling changes across all icons by modifying only the master element
      - Master icon should be positioned with `alpha: "0"` to remain invisible
      - Inherited properties include: width, height, scale, labelColor, badgeTextColor, badgeBGColor, badgeImage, badgeImageScale, badgeImageX, badgeImageY
    
    - **Play/Pause Button Animation System**: For music players requiring state-dependent buttons:
      - Create main button container with both play and pause icons
      - Play icon visible by default, pause icon with `alpha: "0"`
      - Add separate animation controller (hidden with `alpha: "0"`) containing:
        - `ani1`: Detector element with `showWhenPlaying: "triggerOn:showpausebtn, triggerOff:showplaybtn"`
        - `showpausebtn`: Triggers animation `{ PlayIcon: "alpha:0", PauseIcon: "alpha:1" }`
        - `showplaybtn`: Triggers animation `{ PlayIcon: "alpha:1", PauseIcon: "alpha:0" }`
      - Music state automatically triggers appropriate animation to fade between icons
      - Reference `musicexample.json` for exact implementation pattern
- 
**Constraints**:
  - Never resize PresetView.
  - Test for content length and hierarchy integrity.
  - Verify as you build, check data values against `data.txt`

## Additional Notes
- **File Naming**: Save themes in `AIThemes/_<ThemeName>.json`.

- **Accessibility**: Ensure text legibility and sufficient contrast in themes.

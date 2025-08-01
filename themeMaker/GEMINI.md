# Gemini Theme Creation Guide

This guide explains how to create themes for the Designer iOS app, based on the existing structure and conventions in this project. You shouldn't need to Validate the theme if you use the references provided.

## 1. Theme File Basics

-   **Format**: Themes are JSON files.
-   **Root Element**: Every theme must start with a root object called `"PresetView"`. This acts as the main screen or canvas for your widget.
-   **File Location**: New themes should be saved in the `AIThemes/` directory.
-   **File Naming**: Theme filenames must start with an underscore (e.g., `_MyNewTheme.json`).

## 2. The Anatomy of a Theme

A theme is a nested structure of views, starting with `PresetView`.

```json
{
  "PresetView": {
    "type": "UIView",
    "frame": { "x": "0", "y": "0", "width": "screen_width", "height": "screen_height" },
    "children": {
      // Your UI elements go here
    }
  }
}
```

### Key Concepts:

-   **`UIView`**: The basic building block for layouts. It's a container that can hold other views (`children`).
-   **`UILabel`**: A view used to display text.
-   **`BatteryBar`**: A special view to display the device's battery level.
-   **`children`**: An object within a `UIView` that contains named child elements. The names (`hour`, `date`, `container`, etc.) are for your own reference.

## 3. Positioning and Layout (`frame`)

The `frame` property defines the size and position of an element.

-   **`x`, `y`**: The horizontal and vertical position of the top-left corner.
-   **`width`, `height`**: The dimensions of the element.

### Positioning Values:

-   **Absolute**: `"x": "10"`, `"y": "50"`
-   **Screen Dimensions**: `"width": "screen_width"`
-   **Centered**: `"x": "center"`
-   **Relative to Edges**:
    -   `"x": "FR~20"` (20 points from the right edge)
    -   `"y": "FB~50"` (50 points from the bottom edge)
-   **Mathematical Expressions**: You can use simple math, like `"width": "math(screen_width-40)"`.

## 4. Displaying Dynamic Data

To show dynamic information like time, date, or weather, use special data placeholders in the `"data"` property of a `UILabel`.

**Example:**

```json
"data": "It's currently [temperature][temptype] [condition]"
```

### Common Data Fields:

-   Time: `[textHour]`, `[textMinute]`, `[paddeddate]`
-   Date: `[day]`, `[month]`, `[dateordinal]`, `[firstletterday]`
-   Battery: `[batterypercent]`
-   Weather: `[temperature]`, `[temptype]`, `[condition]`, `[windspeed]`, `[windunit]`

**Note**: When making a theme grep to make sure the data value is correct and will pull the right data. All of htem are in the `data.txt` file.

## 5. Styling and Visuals

### Colors

-   **CRITICAL**: All colors **must** be in `rgba()` format. Hex codes are not supported.
-   **Syntax**: `rgba(red, green, blue, alpha)`
-   **Example**: `"textColor": "rgba(255, 255, 255, 1.0)"` for solid white, `"backgroundColor": "rgba(0, 0, 0, 0.5)"` for semi-transparent black.

### Fonts

-   **`fontName`**: Specify the font file (e.g., `"Bebas.ttf"`).
-   **`fontSize`**: The size of the font.
-   **CRITICAL**: You must use fonts available in the Designer system. Refer to `fonts.txt` for a list of valid font names.

### Other Visual Properties:

-   **`cornerRadius`**: Rounds the corners of a `UIView`.
-   **`blurAlpha`**: Creates a frosted glass/blur effect. Apply it to a container `UIView`.
-   **`boxShadow`**: Adds a shadow to a view.
-   **`textAlignment`**: Aligns text within a `UILabel` (`NSTextAlignmentLeft`, `NSTextAlignmentCenter`, `NSTextAlignmentRight`).
    -   **Note**: For text to be centered within a circular or other container view, you **must** set the `textAlignment` property on the `UILabel` itself. Centering the parent `UIView` is not enough.

## 6. How to Create a New Theme: Step-by-Step

1.  **Review References**: Look at the `.json` files in the `theme references/` folder to understand the structure and available properties. `JN07.json` and `JN33.json` are great starting points.
2.  **Create a New File**: Create a new file in the `AIThemes/` directory. The name must start with a leading underscore and end with a random number to ensure uniqueness (e.g., `_MyCoolTheme847.json`).
3.  **Start with `PresetView`**: Begin your file with the standard `PresetView` structure.
4.  **Add UI Elements**:
    -   Create `UIView` containers to organize your layout.
    -   Add `UILabel` elements for text and data.
    -   Position and size them using the `frame` property.
5.  **Apply Styles**:
    -   Set colors (`textColor`, `backgroundColor`) using `rgba()` format.
    -   Choose fonts and sizes.
    -   Add effects like `cornerRadius` and `blurAlpha`.
6.  **Integrate Data**: Populate your `UILabel`s with dynamic data fields (e.g., `[day]`, `[textHour]`).
7.  **Test and Refine**: Test your theme to ensure it looks and functions as expected. Check for text overflow and alignment issues.

By following these guidelines and using the provided reference files, you can create your own custom Designer themes.

## 7. Critical Component Implementations

Certain components require a very specific structure. Always refer to the `theme references/` folder for a working example before implementing them.

### Weather Icons

-   **CRITICAL**: To display a weather icon, you **must** use the `WeatherIcon` type. Do not try to use a `UILabel` or `UIView`.
-   **Implementation**:

    ```json
    "Icon1": {
      "type": "WeatherIcon",
      "frame": { "x": "5", "y": "20", "width": "50", "height": "50" },
      "forecast": "day1", // or "current", "day2", etc.
      "iconSet": "clima" // or another valid icon set
    }
    ```

### Rounded Corners with Blur

-   **CRITICAL**: To create a view that has both rounded corners and a blur effect, you **must** use a nested structure. Applying `cornerRadius` and `blurAlpha` to the same `UIView` will not work correctly; the blur will not be clipped by the corners.
-   **Implementation**:
    1.  **Outer "Clipper" View**: Create a `UIView` that defines the `cornerRadius`. This view's only purpose is to clip its children. It should have a transparent background (`rgba(0,0,0,0)`).
    2.  **Inner "Blur" View**: Inside the clipper, create another `UIView`. This inner view should have the `blurAlpha` property and contain all the content (labels, icons, etc.). Its frame should match the clipper's bounds (e.g., `width: "view_width", height: "view_height"`).

-   **Example Structure**:
    ```json
    "MyClipperView": {
      "type": "UIView",
      "frame": { "x": "center", "y": "center", "width": "300", "height": "200" },
      "cornerRadius": "25",
      "backgroundColor": "rgba(0,0,0,0)", 
      "children": {
        "MyBlurContentView": {
          "type": "UIView",
          "frame": { "x": "0", "y": "0", "width": "view_width", "height": "view_height" },
          "blurAlpha": "0.9",
          "backgroundColor": "rgba(255, 255, 255, 0.15)", 
          "children": {
            
          }
        }
      }
    }
    ```

-   **CRITICAL**: To create a scrollable area, you **must** use `"type": "ScrollView"` (note the capitalization and lack of "UI" prefix).
-   **Properties**:
    -   `"type": "ScrollView"`
    -   `"frame"`: Defines the visible area of the scroll view.
    -   `"contentSize"`: Defines the total scrollable area. If `contentSize.height` is greater than `frame.height`, vertical scrolling will be enabled.
    -   `"userInteraction": "YES"`: Essential for enabling scrolling.
    -   `"children"`: The `ScrollView` should contain a single `UIView` as its direct child. All scrollable content should be placed within this child `UIView`.
    -   `"childOrder"`: Must be explicitly set on the `ScrollView` to list its direct child (the content `UIView`). It must also be set on the content `UIView` to order its children.
-   **Implementation Example**:

    ```json
    "MyScrollView": {
      "type": "ScrollView",
      "frame": { "x": "0", "y": "0", "width": "screen_width", "height": "250" },
      "contentSize": { "width": "screen_width", "height": "500" }, // Example: content is twice as tall as visible frame
      "userInteraction": "YES",
      "childOrder": "ScrollContent",
      "children": {
        "ScrollContent": {
          "type": "UIView",
          "frame": { "x": "0", "y": "0", "width": "screen_width", "height": "500" },
          "childOrder": "Element1,Element2,Element3", // Order of elements within the scrollable area
          "children": {
            "Element1": { /* ... */ },
            "Element2": { /* ... */ },
            "Element3": { /* ... */ }
          }
        }
      }
    }
    ```

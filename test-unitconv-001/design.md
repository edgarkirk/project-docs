# unitconv — Frontend Design Specification

## Layout

Single-page application with a centered card layout on a neutral background.

```
┌──────────────────────────────────────────────────────┐
│                                                      │
│              ┌────────────────────────┐              │
│              │     Unit Converter     │  ← title     │
│              │                        │              │
│              │  ┌──────────────────┐  │              │
│              │  │     123.45       │  │  ← value     │
│              │  └──────────────────┘  │              │
│              │                        │              │
│              │  ┌────────┐  ┌──────┐  │              │
│              │  │ metres ▼│  │feet ▼│  │  ← units    │
│              │  └────────┘  └──────┘  │              │
│              │                        │              │
│              │  [ ⇄ Swap ]            │  ← swap btn  │
│              │                        │              │
│              │  ┌────────────────────┐│              │
│              │  │     Convert        ││  ← action    │
│              │  └────────────────────┘│              │
│              │                        │              │
│              │  ┌────────────────────┐│              │
│              │  │  123.45 metres =   ││              │
│              │  │  404.69 feet       ││  ← result    │
│              │  └────────────────────┘│              │
│              │                        │              │
│              └────────────────────────┘              │
│                                                      │
└──────────────────────────────────────────────────────┘
```

## Components

### Header

- Title: "Unit Converter"
- Subtitle: "Convert between metric and imperial units"
- No navigation — single page app

### Value Input

- Large numeric input field, centered text
- Placeholder: "Enter value"
- Numeric keyboard on mobile (inputmode="decimal")
- Clears on focus if value is "0"

### Unit Selectors

- Two dropdown selects side by side
- Left: "From" unit
- Right: "To" unit
- Options grouped by category:
  - **Length**: metres, feet, kilometres, miles
  - **Volume**: litres, gallons
- Default selection: metres → feet
- When a "From" unit is selected, the "To" dropdown only shows compatible units (same group)

### Swap Button

- Small button between the two unit selectors
- Icon: ⇄ (bidirectional arrow)
- Swaps the "From" and "To" values
- If a result is displayed, the result value becomes the new input value

### Convert Button

- Full-width primary button
- Label: "Convert"
- Disabled state when input is empty or non-numeric
- Loading state: show spinner, disable button

### Result Display

- Appears below the convert button after successful conversion
- Format: "{inputValue} {sourceUnit} = {result} {targetUnit}"
- Large font for the result number
- Subtle animation on appearance (fade in)
- Hidden when no conversion has been performed

### Error Display

- Replaces the result area when an error occurs
- Red/destructive color scheme
- Shows the error message from the API
- Dismissible — clicking the input field or changing units clears the error

## States

### Idle (initial)

- Value input empty, placeholder shown
- Default units selected (metres → feet)
- Convert button disabled
- No result or error displayed

### Ready

- Value entered, valid number
- Units selected
- Convert button enabled

### Loading

- Convert button shows spinner, disabled
- Input and selects remain visible but dimmed

### Success

- Result card appears below with converted value
- Convert button returns to enabled state
- Input retains the value for easy re-conversion

### Validation Error (client-side)

- Non-numeric input: red border on input field, helper text "Please enter a valid number"
- Triggered on blur or on submit attempt
- No API call made

### API Error

- Error card appears in place of result
- Shows API error message
- Red left border or red background tint
- Convert button re-enabled for retry

## Colors

| Element | Color |
|---------|-------|
| Background | `#f5f5f5` (light gray) |
| Card | `#ffffff` (white) with subtle shadow |
| Primary button | `#2563eb` (blue-600) |
| Primary hover | `#1d4ed8` (blue-700) |
| Input border | `#d1d5db` (gray-300) |
| Input focus border | `#2563eb` (blue-600) |
| Result text | `#111827` (gray-900) |
| Result accent | `#059669` (emerald-600) |
| Error background | `#fef2f2` (red-50) |
| Error border | `#ef4444` (red-500) |
| Error text | `#991b1b` (red-800) |
| Subtitle/helper | `#6b7280` (gray-500) |

## Typography

| Element | Size | Weight |
|---------|------|--------|
| Title | 24px | 700 (bold) |
| Subtitle | 14px | 400 (normal) |
| Input value | 32px | 500 (medium) |
| Unit labels | 14px | 500 (medium) |
| Convert button | 16px | 600 (semibold) |
| Result value | 28px | 700 (bold) |
| Result label | 14px | 400 (normal) |
| Error message | 14px | 500 (medium) |

## Spacing

- Card max-width: 420px
- Card padding: 32px
- Card border-radius: 12px
- Input fields: 48px height, 12px horizontal padding
- Button: 48px height, full width
- Gap between elements: 16px
- Gap between unit selectors: 12px
- Result card margin-top: 24px

## Responsive Behavior

- Card is centered on desktop (max-width: 420px)
- On mobile (< 480px): card takes full width with 16px margin
- Unit selectors stack horizontally (they're narrow enough)
- Touch targets: minimum 44px for all interactive elements

## Accessibility

- All inputs have associated labels (visually hidden if needed)
- Unit dropdowns have aria-label: "Convert from" / "Convert to"
- Error messages use role="alert" for screen reader announcement
- Result uses aria-live="polite" for dynamic updates
- Color contrast meets WCAG AA (4.5:1 for text)
- Focus ring visible on all interactive elements
- Swap button has aria-label: "Swap units"

## Unit Display Names

| API value | Display name |
|-----------|-------------|
| metres | Metres (m) |
| feet | Feet (ft) |
| kilometres | Kilometres (km) |
| miles | Miles (mi) |
| litres | Litres (L) |
| gallons | Gallons (gal) |

## Interactions

### Convert Flow
1. User enters value → "Ready" state
2. User clicks "Convert" → "Loading" state
3. API responds 200 → "Success" state, result shown
4. API responds 400 → "API Error" state, error shown

### Swap Flow
1. User clicks ⇄ swap button
2. "From" and "To" units swap
3. If a result exists, the result value moves to the input field
4. Previous result clears
5. Auto-convert if value is present (optional enhancement)

### Unit Change Flow
1. User changes "From" unit
2. "To" dropdown filters to compatible units only
3. If current "To" selection is incompatible, reset to first compatible option
4. Previous result clears
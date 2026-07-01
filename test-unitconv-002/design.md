# unitconv — UI Design

## Page Layout

```
┌─────────────────────────────────────────────┐
│  Unit Converter                             │
├─────────────────────────────────────────────┤
│                                             │
│  ┌─────────────────────────────────────┐    │
│  │         Conversion Form             │    │
│  │                                     │    │
│  │  Value:  [_______________]          │    │
│  │                                     │    │
│  │  From:   [▼ Select unit  ]          │    │
│  │                                     │    │
│  │  To:     [▼ Select unit  ]          │    │
│  │                                     │    │
│  │  [ Convert ]                        │    │
│  │                                     │    │
│  │  ┌─────────────────────────────┐    │    │
│  │  │ 1 metres = 3.28084 feet    │    │    │
│  │  └─────────────────────────────┘    │    │
│  │                                     │    │
│  │  (error message displayed here)     │    │
│  └─────────────────────────────────────┘    │
│                                             │
│  ┌─────────────────────────────────────┐    │
│  │        Conversion History           │    │
│  │                                     │    │
│  │  • 1 metres → 3.28084 feet         │    │
│  │  • 5 kilometres → 3.10686 miles     │    │
│  │  • 2 litres → 0.528344 gallons     │    │
│  └─────────────────────────────────────┘    │
│                                             │
└─────────────────────────────────────────────┘
```

## Component Tree

```mermaid
graph TD
  App["App"]
  App --> Header["Header<br/><i>Title: Unit Converter</i>"]
  App --> ConversionForm["ConversionForm"]
  App --> ConversionHistory["ConversionHistory"]

  ConversionForm --> ValueInput["ValueInput<br/><i>type=number</i>"]
  ConversionForm --> SourceUnitSelect["SourceUnitSelect<br/><i>dropdown, from /api/units</i>"]
  ConversionForm --> TargetUnitSelect["TargetUnitSelect<br/><i>dropdown, from /api/units</i>"]
  ConversionForm --> ConvertButton["ConvertButton"]
  ConversionForm --> ResultDisplay["ResultDisplay<br/><i>shows converted value</i>"]
  ConversionForm --> ErrorDisplay["ErrorDisplay<br/><i>shows validation errors</i>"]

  ConversionHistory --> HistoryItem["HistoryItem[]<br/><i>source → result</i>"]
```

## User Flow

```mermaid
sequenceDiagram
  participant User
  participant Frontend
  participant Backend

  Note over Frontend: Page loads
  Frontend->>Backend: GET /api/units
  Backend-->>Frontend: Unit[] (6 units)
  Frontend->>Frontend: Populate dropdowns

  Note over User: Fills form
  User->>Frontend: Enter value, select units
  User->>Frontend: Click Convert
  Frontend->>Backend: POST /api/convert
  alt Valid request
    Backend-->>Frontend: 200 ConversionResult
    Frontend->>Frontend: Show result, add to history
  else Invalid request
    Backend-->>Frontend: 400 ValidationError
    Frontend->>Frontend: Show error message
  end
```

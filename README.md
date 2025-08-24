# Qlock - Word Clock Display

A beautiful web-based word clock that displays time using words instead of numbers. The clock is available in German (primary) and Swedish languages.

## Overview

Qlock is a digital word clock implementation that represents time by highlighting words in a grid of letters. Instead of showing "3:25", it displays "ES IST FÜNF VOR HALB VIER" (German) or equivalent phrases in Swedish.

## Features

- **Multi-language support**: German (primary) and Swedish
- **Real-time updates**: Clock updates every second with live time display
- **Mobile-responsive design**: Optimized for various screen sizes
- **Visual minute indicators**: Corner dots show individual minutes within 5-minute intervals
- **Clean typography**: Uses Helvetica font for clear readability
- **Color-coded display**: Active words are highlighted in white against a red background

## File Structure

```
qlock/
├── index.html          # German version (primary)
├── index_se.html       # Swedish version
└── img/               # Favicon and app icons
    ├── android-chrome-192x192.png
    ├── android-chrome-512x512.png
    ├── apple-touch-icon.png
    ├── browserconfig.xml
    ├── favicon-16x16.png
    ├── favicon-32x32.png
    ├── favicon.ico
    ├── mstile-150x150.png
    ├── safari-pinned-tab.svg
    └── site.webmanifest
```

## Technical Implementation

### Language Grids

Both versions use letter grids containing all possible words needed to display time:

#### German Grid (11x10):
```
E S K I S T A F Ü N F
Z E H N Z W A N Z I G
D R E I V I E R T E L
V O R F U N K N A C H
H A L B A E L F Ü N F
E I N S X A M Z W E I
D R E I P M J V I E R
S E C H S N L A C H T
S I E B E N Z W Ö L F
Z E H N E U N K U H R
```

#### Swedish Grid (11x10):
```
K L O C K A N T Ä R K
F E M Y I S T I O N I
K V A R T Q I E N Z O 
T J U G O L I V I P M 
Ö V E R K A M H A L V
E T T U S V L X T V Å
T R E M Y K Y F Y R A
F E M S F L O R S E X
S J U Ä T T A I N I O
T I O E L V A T O L V
```

### Time Calculation Logic

1. **Current time extraction**: Gets hours and minutes from system time
2. **5-minute intervals**: Minutes are rounded to nearest 5-minute interval
3. **Hour adjustment**: Hours are incremented for times past the half-hour
4. **Word selection**: Appropriate words are selected based on the time
5. **Grid highlighting**: Selected words are highlighted in the letter grid

### Word Patterns

#### German Time Expressions:
- **Exact hours**: "ES IST [hour] UHR"
- **5 minutes past**: "ES IST FÜNF NACH [hour]"
- **Quarter past**: "ES IST VIERTEL NACH [hour]"
- **Half past**: "ES IST HALB [next hour]"
- **Quarter to**: "ES IST VIERTEL VOR [next hour]"

#### Swedish Time Expressions:
- **Exact hours**: "KLOCKAN ÄR [hour]"
- **5 minutes past**: "KLOCKAN ÄR FEM ÖVER [hour]"
- **Quarter past**: "KLOCKAN ÄR KVART ÖVER [hour]"
- **Half past**: "KLOCKAN ÄR HALV [next hour]"
- **Quarter to**: "KLOCKAN ÄR KVART I [next hour]"

## Visual Design

### Color Scheme
- **Background**: Red (#e63946)
- **Inactive text**: Light red (#f55 or #f66)
- **Active text**: White (#fff)
- **Theme color**: White (#ffffff) for PWA

### Layout
- **Centered display**: Clock is centered both horizontally and vertically
- **Grid format**: Letters arranged in a table format
- **Corner indicators**: Four corner dots indicate individual minutes (0-4)
- **Responsive sizing**: German version uses larger font (28px) for better visibility

### Corner Minute Indicators
The four corners show individual minutes within each 5-minute interval:
- **Top-left**: Minute 1
- **Top-right**: Minute 2  
- **Bottom-right**: Minute 3
- **Bottom-left**: Minute 4

## Usage

### Local Development
1. Clone or download the project
2. Open `index.html` (German) or `index_se.html` (Swedish) in a web browser
3. The clock will display the current time

### Web Deployment
1. Upload all files to a web server
2. Access via browser - the app includes PWA manifest for mobile installation
3. Can be added to home screen on mobile devices

## Browser Compatibility
- Modern browsers with JavaScript enabled
- Mobile-optimized with viewport meta tags
- PWA features for offline usage

## Configuration Options

### Switching Languages
- Use `index.html` for German (primary)
- Use `index_se.html` for Swedish
- Main `index.html` defaults to German with real-time updates

### Customizing Updates
- German version (`index.html`): Updates every 1000ms (real-time)
- Swedish version (`index_se.html`): Static time display (interval commented out)

## Code Structure

### Key Functions

#### `renderClock()`
Main function that:
1. Gets current time
2. Calculates which words to highlight
3. Generates HTML for the clock display
4. Returns formatted HTML string

#### Word Mapping
- `trans` object maps words to their grid positions
- `grid` object contains language-specific configurations
- `order` array defines time expressions for each 5-minute interval

### Key Variables
- `width`, `height`: Grid dimensions
- `map`: Array tracking which grid positions to highlight
- `matrix`: The letter grid as a string or array
- `time`, `hour`, `minute5`: Current time components

## Contributing

To add a new language:
1. Create letter grid with all required time words
2. Define word-to-position mapping
3. Create time expression patterns
4. Add language configuration to the grid object

## License

This project appears to be a personal/educational word clock implementation. Check with the original author for specific licensing terms.

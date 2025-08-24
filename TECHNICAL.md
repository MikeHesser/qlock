# Qlock Technical Documentation

## Architecture Overview

Qlock uses a client-side JavaScript implementation to render a word-based clock display. The architecture consists of three main components:

1. **Language Configuration System**: Defines grid layouts and time expressions
2. **Time Processing Engine**: Converts current time into word selections
3. **HTML Rendering System**: Generates the visual display

## Data Structures

### Grid Configuration (`grid` object)

```javascript
grid = {
  [language]: {
    grid: string,     // Flattened letter grid
    hours: array,     // Hour names in order [12, 1, 2, ...]
    begin: array,     // Starting words ["ES", "IST"]
    order: array      // Time expression patterns per 5-min interval
  }
}
```

#### Grid Layout
- **Dimensions**: 11 columns × 10 rows = 110 characters
- **Storage**: Single string with characters in row-major order
- **Access**: `position = x + y * width`

### Translation Map (`trans` object)

Maps word strings to their positions in the grid:

```javascript
trans = {
  'WORD': [pos1, pos2, pos3, ...],  // Array of grid positions
  'WORD2': [pos1, pos2, ...]        // Alternative positions for duplicates
}
```

### Time Expression Orders

Array indexed by 5-minute intervals (0-11):

```javascript
order = [
  [],                           // :00 - exact hour
  ['FÜNF', 'NACH'],            // :05 - five past
  ['ZEHN', 'NACH'],            // :10 - ten past
  ['VIERTEL', 'NACH'],         // :15 - quarter past
  ['ZWANZIG', 'NACH'],         // :20 - twenty past
  ['FÜNF', 'VOR', 'HALB'],     // :25 - five to half
  ['HALB'],                    // :30 - half
  ['FÜNF', 'NACH', 'HALB'],    // :35 - five past half
  ['ZWANZIG', 'VOR'],          // :40 - twenty to
  ['VIERTEL', 'VOR'],          // :45 - quarter to
  ['ZEHN', 'VOR'],             // :50 - ten to
  ['FÜNF', 'VOR']              // :55 - five to
]
```

## Core Algorithm

### Time Processing Flow

```javascript
function renderClock() {
  // 1. Get current time
  let time = new Date()
  let hour = time.getHours()
  let minute5 = Math.floor(time.getMinutes() / 5)
  let minute = time.getMinutes() % 5
  
  // 2. Adjust hour for future expressions
  if (minute5 > 4) {
    hour++;
  }
  hour = hour % 12
  
  // 3. Build word list
  let text = [...setting.begin]
  text.push(...setting.order[minute5])
  text.push(setting.hours[hour])
  
  // 4. Map words to grid positions
  let map = new Array(height * width)
  for (word of text) {
    let positions = findWordPositions(word)
    positions.forEach(pos => map[pos] = 1)
  }
  
  // 5. Generate HTML
  return generateHTML(map, minute)
}
```

### Word Position Resolution

For duplicate words in the grid, the algorithm uses sequential positioning:

```javascript
let maxix = 0
for (let j = 0; j < text.length; j++) {
  let word = text[j]
  let ix = setting.grid.indexOf(word, maxix)  // Find next occurrence
  if (ix === -1) continue
  
  for (let i = 0; i < word.length; i++) {
    map[ix + i] = 1
    maxix = Math.max(maxix, ix + i)
  }
}
```

## Language-Specific Implementations

### German Version Differences

1. **Matrix Structure**: Uses 2D array instead of flattened string
2. **Hour Logic**: Special handling for "EIN" vs "EINS" at exact hours
3. **Update Frequency**: Real-time updates every 1000ms
4. **Word Resolution**: Uses `trans` object with fallback to `word + '2'`

```javascript path=null start=null
// German hour handling
let hourText = hours[hour]
if (hourText === 'EIN' && minute5 === 0) {
  hourText = 'EINS'  // "One o'clock" uses EINS
}
if (minute5 === 0) {
  text.push('UHR')   // Add "o'clock" for exact hours
}
```

### Swedish Version Features

1. **Unified Configuration**: Both languages in single `grid` object
2. **String-based Grid**: Flattened string representation
3. **Static Display**: Currently shows fixed time (update interval commented)

## HTML Generation

### Table Structure

```html
<table>
  <tr>
    <td class="active">•</td>     <!-- Minute indicator -->
    <td colspan="11"></td>
    <td class="active">•</td>
  </tr>
  <!-- Letter grid rows -->
  <tr>
    <td>&nbsp;</td>               <!-- Side padding -->
    <td class="active">E</td>     <!-- Grid letters -->
    ...
    <td>&nbsp;</td>
  </tr>
  ...
  <tr>
    <td class="active">•</td>     <!-- Bottom indicators -->
    <td colspan="11"></td>
    <td class="active">•</td>
  </tr>
</table>
```

### CSS Classes

- **`.active`**: White text color for highlighted elements
- **Default**: Red-tinted text for inactive elements
- **`.centered`**: Absolute positioning for screen center

## Minute Indicators

Corner dots show individual minutes within 5-minute intervals:

```javascript path=null start=null
// Minute indicator logic
let indicators = [
  minute > 0,  // Top-left
  minute > 1,  // Top-right
  minute > 2,  // Bottom-right
  minute > 3   // Bottom-left
]
```

## Performance Considerations

### Optimization Strategies

1. **Pre-computed Mappings**: Word positions calculated once
2. **Minimal DOM Updates**: Only innerHTML replacement
3. **Efficient Grid Access**: Direct position calculation
4. **Memory Management**: Fixed-size arrays reused

### Update Frequency

- **German**: 1000ms intervals for real-time updates
- **Swedish**: Single render (interval disabled)
- **Recommendation**: 60000ms (1 minute) for battery savings

## Browser Compatibility

### Required Features
- ES6 array methods (`forEach`, `push`)
- `Date` object and time methods
- DOM manipulation (`getElementById`, `innerHTML`)
- CSS transforms and positioning

### PWA Features
- Web App Manifest for installation
- Favicon set for various platforms
- Viewport meta tags for mobile optimization

## Extensibility

### Adding New Languages

1. **Create Letter Grid**: Design 11×10 grid with all time words
2. **Define Word Mappings**: Map each word to grid positions
3. **Set Time Patterns**: Create `order` array for time expressions
4. **Add Configuration**: Extend `grid` object with new language

```javascript path=null start=null
grid.newlang = {
  grid: 'LETTERSGRIDSTRINGHERE...',
  hours: ['TWELVE', 'ONE', 'TWO', ...],
  begin: ['IT', 'IS'],
  order: [[], ['FIVE', 'PAST'], ...]
}
```

### Customization Options

- **Colors**: Modify CSS color variables
- **Fonts**: Change font-family in CSS
- **Grid Size**: Adjust dimensions (requires grid redesign)
- **Update Rate**: Modify `setInterval` timing

## Debugging Tools

### Console Output
The Swedish version includes debug logging:
```javascript path=null start=null
console.log(JSON.stringify(text))  // Shows selected words
```

### Testing Time Values
To test specific times, modify the `time` variable:
```javascript path=null start=null
let time = new Date('2023-01-01 15:25:00')  // Test 3:25 PM
```

## Known Limitations

1. **Fixed Grid Size**: 11×10 constraint limits word placement
2. **5-Minute Granularity**: Time expressions rounded to 5-minute intervals
3. **Language Switching**: Requires separate HTML files
4. **No Time Zones**: Uses local system time only
5. **Static Resources**: No dynamic loading of language configs

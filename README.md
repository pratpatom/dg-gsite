# Image Presentation Deck

## Quick Setup

1. **Add your slide images** to the `assets/` folder:
   - slide1.jpg
   - slide2.jpg
   - slide3.jpg
   - etc.

2. **Edit the slideImages array** in index.html (around line 172) to match your image filenames:
   ```javascript
   const slideImages = ref([
       './assets/slide1.jpg',
       './assets/slide2.jpg',
       './assets/slide3.jpg',
       // Add more slides here...
   ]);
   ```

## Features

- **Simple Navigation**: Arrow keys, space bar, or click navigation buttons
- **Mobile Support**: Touch/swipe gestures
- **Smooth Animations**: GSAP-powered slide transitions
- **Responsive Images**: Automatically scales to fit screen
- **Error Handling**: Shows helpful message if images fail to load
- **Keyboard Shortcuts**:
  - `→` or `Space`: Next slide
  - `←`: Previous slide
  - `Home`: First slide
  - `End`: Last slide
  - `Esc`: Close error dialogs

## File Structure
```
/
├── index.html          # Main presentation file
├── assets/            # Your slide images go here
│   ├── slide1.jpg
│   ├── slide2.jpg
│   └── ...
└── README.md          # This file
```

## Supported Image Formats
- JPG/JPEG
- PNG
- WebP
- SVG

## Tips
- Use consistent image dimensions for best results
- Keep file sizes reasonable for faster loading
- Use descriptive filenames for easier management
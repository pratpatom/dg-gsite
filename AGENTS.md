# Single Page HTML Application Project
This is a single-file HTML application powered by CDN-hosted libraries for maximum performance and simplicity. The project creates data-driven, animated web experiences without build tools or complex deployment.

## Project Structure
- `index.html` - Main application file containing all HTML, CSS, and JavaScript
- `data/` - JSON/CSV data files for visualization and content
- `assets/` - Static assets (images, fonts, additional resources)
- `docs/` - Documentation and examples

## Technology Stack
- **Vue.js 3** (CDN): Reactive UI framework for component-based architecture
- **Tailwind CSS** (CDN): Utility-first styling with full design system
- **D3.js** (CDN): Data manipulation and SVG generation for visualizations
- **GSAP** (CDN): High-performance animations and timeline management

## Code Standards
- Use ES6+ modules with `<script type="module">` for modern JavaScript
- Implement Vue 3 Composition API for reactive data and computed properties
- Leverage Tailwind's utility classes for styling - avoid custom CSS unless necessary
- Use D3.js for data binding, scales, and SVG element creation
- Implement GSAP for smooth animations, transitions, and timeline orchestration
- All code must be contained within the single HTML file using appropriate script tags

## Architecture Patterns
- **Component Organization**: Define Vue components inline using template literals or x-template approach
- **Data Flow**: Use Vue's reactive data system with D3's data binding for seamless updates
- **Animation Pipeline**: Coordinate GSAP animations with Vue lifecycle hooks and D3 data updates
- **Responsive Design**: Mobile-first approach using Tailwind's responsive utilities

## Performance Guidelines
- Load all CDN libraries from reliable sources (jsDelivr, unpkg, or official CDNs)
- Use specific version numbers for consistency and caching
- Implement lazy loading for heavy data visualizations
- Optimize SVG elements and minimize DOM manipulations
- Cache computed values and use Vue's reactivity efficiently

## Data Visualization Conventions
- Use D3.js scales and layouts for data transformation
- Create reusable chart components with Vue + D3 integration
- Implement smooth data transitions using GSAP's morphing capabilities
- Follow accessibility guidelines for charts and interactive elements
- Support both static and real-time data sources

## Animation Standards
- Use GSAP's Timeline for complex, choreographed animations
- Implement enter/exit animations for dynamic content
- Create smooth transitions between data states
- Use CSS transforms and opacity for hardware acceleration
- Coordinate animations with user interactions and data updates

## Development Workflow
- Test across modern browsers with ES6+ support
- Use browser dev tools for debugging and performance profiling
- Validate HTML5 semantic structure and accessibility
- Test responsive behavior across device sizes
- Ensure smooth 60fps animations on target devices

# Best Practices - Single Page HTML Applications

## Architecture Guidelines

### Component Lifecycle Management

#### ✅ DO
```javascript
// Single, comprehensive onMounted
onMounted(async () => {
    // 1. Setup DOM listeners
    document.addEventListener('keydown', handleKeyPress);
    
    // 2. Initialize data with timeout
    const timeout = setTimeout(fallbackHandler, 10000);
    
    // 3. Load external resources
    try {
        await loadData();
        clearTimeout(timeout);
    } catch (error) {
        clearTimeout(timeout);
        handleError(error);
    }
});
```

#### ❌ DON'T
```javascript
// Multiple conflicting lifecycle hooks
onMounted(async () => { await loadData(); });
onMounted(() => { setupListeners(); }); // DUPLICATE!
```

### Animation Safety Patterns

#### ✅ DO - Defensive Animation
```javascript
mounted() {
    // Validate data exists
    if (!this.data || !Array.isArray(this.data.items)) return;
    
    // Filter valid refs
    const validRefs = [this.$refs.title, this.$refs.subtitle].filter(Boolean);
    if (validRefs.length === 0) return;
    
    // Safe animation
    gsap.set(validRefs, { opacity: 0 });
    
    // Individual ref validation
    if (this.$refs.title) {
        gsap.to(this.$refs.title, { opacity: 1, duration: 0.6 });
    }
}
```

#### ❌ DON'T - Assume Refs Exist
```javascript
mounted() {
    // Dangerous - refs might not exist
    gsap.set([this.$refs.title, this.$refs.subtitle], { opacity: 0 });
    gsap.to(this.$refs.title, { opacity: 1 }); // Could fail
}
```

### Data Loading Strategies

#### ✅ DO - Robust Loading
```javascript
const loadData = async () => {
    const controller = new AbortController();
    const timeoutId = setTimeout(() => controller.abort(), 10000);
    
    try {
        const response = await fetch('./data.json', {
            signal: controller.signal
        });
        
        if (!response.ok) {
            throw new Error(`HTTP ${response.status}: ${response.statusText}`);
        }
        
        const data = await response.json();
        
        // Validate data structure
        if (!validateDataStructure(data)) {
            throw new Error('Invalid data structure');
        }
        
        return data;
    } catch (error) {
        if (error.name === 'AbortError') {
            console.warn('Data loading timeout');
        }
        throw error;
    } finally {
        clearTimeout(timeoutId);
    }
};
```

#### ❌ DON'T - Naive Loading
```javascript
const loadData = async () => {
    const response = await fetch('./data.json'); // No timeout
    return await response.json(); // No validation
};
```

### Error Handling Hierarchy

#### Level 1: Component Level
```javascript
mounted() {
    try {
        this.initializeComponent();
    } catch (error) {
        console.error('Component initialization failed:', error);
        this.showErrorState();
    }
}
```

#### Level 2: Data Level
```javascript
const loadPresentationData = async () => {
    try {
        return await fetchData();
    } catch (error) {
        console.error('Data loading failed:', error);
        return getFallbackData();
    }
};
```

#### Level 3: Application Level
```javascript
window.addEventListener('error', (event) => {
    console.error('Global error:', event.error);
    showGlobalErrorMessage();
});

window.addEventListener('unhandledrejection', (event) => {
    console.error('Unhandled promise rejection:', event.reason);
    event.preventDefault();
});
```

## Performance Optimization

### Lazy Loading Components
```javascript
const components = {
    TitleSlide: {
        // Inline component definition
        template: `...`,
        mounted() {
            // Only initialize when component is actually used
            this.$nextTick(() => {
                this.initializeAnimations();
            });
        }
    }
};
```

### Efficient Animation Patterns
```javascript
// Batch DOM queries
const getAllRefs = () => {
    const refs = {};
    Object.keys(this.$refs).forEach(key => {
        if (this.$refs[key]) refs[key] = this.$refs[key];
    });
    return refs;
};

// Reusable animation timeline
const createSlideTimeline = (refs) => {
    const tl = gsap.timeline();
    
    Object.entries(refs).forEach(([key, element], index) => {
        tl.to(element, {
            opacity: 1,
            y: 0,
            duration: 0.6
        }, index * 0.1);
    });
    
    return tl;
};
```

### Memory Management
```javascript
// Proper cleanup in onUnmounted
onUnmounted(() => {
    // Remove event listeners
    document.removeEventListener('keydown', handleKeyPress);
    
    // Kill GSAP animations
    gsap.killTweensOf('*');
    
    // Clear timeouts
    if (loadingTimeout) clearTimeout(loadingTimeout);
});
```

## Testing Strategies

### Unit Testing Components
```javascript
// Test component mounting with missing data
test('handles missing data gracefully', () => {
    const wrapper = mount(TitleSlide, {
        props: { data: null }
    });
    
    expect(wrapper.exists()).toBe(true);
    expect(console.error).not.toHaveBeenCalled();
});

// Test animation safety
test('animates only existing refs', () => {
    const wrapper = mount(SlideComponent);
    
    // Mock GSAP
    const gsapSpy = jest.spyOn(gsap, 'to');
    
    wrapper.vm.$refs.title = null; // Simulate missing ref
    wrapper.vm.initializeAnimations();
    
    expect(gsapSpy).not.toHaveBeenCalledWith(null, expect.any(Object));
});
```

### Integration Testing
```javascript
// Test complete loading flow
test('handles complete loading cycle', async () => {
    // Mock fetch failure
    global.fetch = jest.fn().mockRejectedValue(new Error('Network error'));
    
    const app = createApp(MainApp);
    await app.mount('#app');
    
    // Should use fallback data
    expect(app.presentationData.value.slides).toBeDefined();
});
```

### End-to-End Testing
```javascript
// Cypress test for loading states
describe('Presentation Loading', () => {
    it('shows loading spinner then content', () => {
        cy.visit('/');
        cy.get('.loading-spinner').should('be.visible');
        cy.get('.slide-container', { timeout: 15000 }).should('be.visible');
        cy.get('.loading-spinner').should('not.exist');
    });
    
    it('handles data loading failure', () => {
        cy.intercept('GET', '/data/presentation-data.json', { forceNetworkError: true });
        cy.visit('/');
        cy.contains('Demo Title').should('be.visible'); // Fallback content
    });
});
```

## Code Review Checklist

### Architecture ✓
- [ ] Single `onMounted` per component
- [ ] No duplicate lifecycle hooks
- [ ] Clear separation of concerns
- [ ] Proper error boundaries

### Safety ✓
- [ ] All refs validated before use
- [ ] Data structures validated before access
- [ ] Timeout mechanisms for async operations
- [ ] Graceful degradation for failures

### Performance ✓
- [ ] Minimal DOM queries
- [ ] Efficient animation patterns
- [ ] Proper cleanup in unmount
- [ ] No memory leaks

### Maintainability ✓
- [ ] Clear component naming
- [ ] Documented complex logic
- [ ] Consistent error handling
- [ ] Testable code structure

## Common Anti-Patterns to Avoid

### ❌ The "Shotgun" Approach
```javascript
// Multiple attempts without understanding
onMounted(() => { loadData(); });
onMounted(() => { loadData(); }); // Duplicate
mounted() { this.loadData(); }    // Wrong lifecycle
```

### ❌ The "Optimistic" Pattern
```javascript
// Assuming everything works
mounted() {
    this.data.items.forEach(item => {
        gsap.to(this.$refs[item.id], { opacity: 1 }); // Assumes refs exist
    });
}
```

### ❌ The "Silent Failure" Pattern
```javascript
// Errors are swallowed
try {
    await loadData();
} catch (error) {
    // Silent failure - user never knows what happened
}
```

---

**Document Version**: 1.0  
**Last Updated**: September 17, 2025  
**Next Review**: Monthly
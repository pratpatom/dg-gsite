# Troubleshooting Guide - Single Page HTML Applications

## Quick Diagnostics

### Page Hanging Issues
```bash
# Check browser console for errors
# Look for these common patterns:

# 1. Duplicate lifecycle hooks
grep -n "onMounted" index.html
# Should show only ONE per component

# 2. Missing null checks in animations
grep -A5 -B5 "gsap\." index.html
# Verify refs are validated before use

# 3. Data loading without timeout
grep -A10 "fetch(" index.html
# Should include timeout mechanism
```

### Common Fix Patterns

#### 1. Safe Component Mounting
```javascript
// BAD
mounted() {
    gsap.set([this.$refs.title], { opacity: 0 });
}

// GOOD
mounted() {
    if (!this.$refs.title) return;
    gsap.set([this.$refs.title], { opacity: 0 });
}
```

#### 2. Data Loading Safety
```javascript
// BAD
onMounted(async () => {
    const data = await fetch('./data.json');
    // No timeout, no error handling
});

// GOOD
onMounted(async () => {
    const timeout = setTimeout(() => {
        isLoading.value = false;
        useFallbackData();
    }, 10000);
    
    try {
        const data = await fetch('./data.json');
        clearTimeout(timeout);
    } catch (error) {
        clearTimeout(timeout);
        handleError(error);
    }
});
```

#### 3. Dynamic Ref Validation
```javascript
// BAD
this.data.items.forEach((_, index) => {
    gsap.to(this.$refs['item' + index], { opacity: 1 });
});

// GOOD
this.data.items.forEach((_, index) => {
    const ref = this.$refs['item' + index];
    if (ref) {
        gsap.to(ref, { opacity: 1 });
    }
});
```

### Emergency Fixes

#### Quick Page Unfreeze
1. Add loading timeout:
```javascript
setTimeout(() => { isLoading.value = false; }, 5000);
```

2. Disable animations temporarily:
```javascript
// Comment out GSAP calls to isolate issue
// gsap.to(element, { ... });
```

3. Use minimal fallback data:
```javascript
const emergencyData = {
    meta: { title: "Emergency Mode" },
    slides: { slide1: { title: "Loading Failed" } }
};
```

### Performance Monitoring

#### Browser DevTools Checklist
- [ ] Network tab: Check for failed requests
- [ ] Console: Look for JavaScript errors
- [ ] Performance: Check for infinite loops
- [ ] Elements: Verify DOM structure renders

#### Common Error Messages
- `Cannot read property of undefined` → Add null checks
- `GSAP target not found` → Validate refs before animation
- `Infinite update loop detected` → Check reactive dependencies
- `Failed to fetch` → Add network error handling

### Development Server Setup
```bash
# Start development server
cd /path/to/project
python3 -m http.server 8000

# Test data endpoints
curl http://localhost:8000/data/presentation-data.json

# Monitor server logs
python3 -m http.server 8000 > server.log 2>&1 &
tail -f server.log
```

### Code Quality Checks

#### Pre-deployment Verification
```bash
# Check for duplicate lifecycle hooks
grep -c "onMounted" index.html
# Should be minimal (one per logical component)

# Verify all refs are safely handled
grep -B2 -A2 "\$refs\." index.html | grep -v "if.*\$refs"
# Should show validation patterns

# Check for error handling
grep -c "try.*catch\|\.catch(" index.html
# Should have error handling for async operations
```

### Debugging Tools

#### Console Debug Commands
```javascript
// Check Vue component state
app._instance.ctx.$data

// Monitor loading state
setInterval(() => console.log('Loading:', isLoading.value), 1000);

// Test animations individually
gsap.to('#test-element', { opacity: 0.5, duration: 1 });

// Validate data structure
console.log('Data valid:', !!presentationData.value.slides);
```

---

**Last Updated**: September 17, 2025  
**Maintainer**: Development Team
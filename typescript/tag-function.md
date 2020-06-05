# Tag Function

* Takes a static string literal array as first argument
* Value supplied for each placeholder expression passed in as remaining arguments
  * Use rest parameter to convert into a second array
* Can be used to establish templating languages

```typescript
function htmlEscape(literals: TemplateStringsArray, ...placeholders: string[]) {
    let result = '';
    
    for (let i = 0; i < placeholders.length; i += 1) {
        result += literals[i];
        result += placeholders[i]
            .replace(/&/g, '&amp;')
            .replace(/"/g, '&quot;')
            .replace(/'/g, '&#39;')
            .replace(/</g, '&lt;')
            .replace(/>/g, '&gt;');
    }
    
    result += literals[literals.length - 1];
    return result;
}
```


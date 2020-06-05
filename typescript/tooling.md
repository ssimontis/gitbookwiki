# Tooling

### ESLint - Replaces TSLint

`yarn add eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin eslint-plugin-functional eslint-config-airbnb --save-dev`

{% tabs %}
{% tab title=".eslint.config.js" %}
```javascript
module.exports = {
  parser: '@typescript-eslint/parser',
  parserOptions: {
    project: './tsconfig.json',
  },
  plugins: ['@typescript-eslint', 'functional'],
  extends: [
  "airbnb",
      "eslint:recommended",
    "plugin:@typescript-eslint/eslint-recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:@typescript-eslint/recommended-requiring-type-checking",
    "plugin:functional/external-recommended",
    "plugin:functional/recommended"
  ],
  rules:  {
    // Overwrite rules specified from the extended configs e.g. 
    // "@typescript-eslint/explicit-function-return-type": "off",
  }
}
```
{% endtab %}

{% tab title="package.json" %}
```
{
  "scripts": {
    "lint": "eslint \"src/**\""
  }
}
```
{% endtab %}
{% endtabs %}

### `Changelog`

```yarn add standard-version -D```

{% tabs %}
{% tab title="package.json" %}
```javascript
{
  "scripts": {
    "release": "standard-version",
     "postrelease": "git push --follow-tags origin master && npm publish"
  }
}
```
{% endtab %}

{% tab title="" %}

{% endtab %}
{% endtabs %}

### Husky

`yarn add husky -D`

```text
{
    "scripts": {
        "precommit": "yarn prettier:write"
    }
}
```

### Prettier

`yarn add prettier -D`

{% tabs %}
{% tab title="package.json" %}
```text
   "prettier:base": "prettier --parser typescript --single-quote",
    "prettier:check": "npm run prettier:base -- --list-different \"src/**/*.{ts,tsx}\"",
    "prettier:write": "npm run prettier:base -- --write \"src/**/*.{ts,tsx}\""
```
{% endtab %}
{% endtabs %}

### Starter Template

{% embed url="https://github.com/jsynowiec/node-typescript-boilerplate" %}

### OpenAPI generated API Docs

{% embed url="https://redocly.github.io/redoc/" %}




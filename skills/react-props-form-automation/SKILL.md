---
name: React Props Form Automation
description: Use when automating React form inputs via JavaScript (browser console or injected JS) and standard DOM events don't trigger React state updates, or when filling inputs that use controlled components with synthetic event handlers.
icon: icon.svg
metadata:
  category: workflow/process
  family: workflow
  lifecycle: active
  canonical_slug: react-props-form-automation
  icon_style: craft-category-glyph-v1
---

# React `__reactProps` Form Automation

## Overview

React controlled inputs ignore native DOM `input.value = x` because React tracks state internally via synthetic event handlers attached to the fiber. To trigger React state updates programmatically, you must find the React fiber's prop handler and call it directly.

## Why `input.value = x` Fails

React overrides `input.value` via `Object.defineProperty` and reads it through the fiber. Setting `.value` directly bypasses React's `onChange` reconciler — the value visually appears but React's internal state doesn't update, so form submission sends the old value.

## The Pattern

```javascript
// 1. Find the input element
const input = document.querySelector('input[placeholder="Email"]');

// 2. Get its React fiber key (varies by React version)
const fiberKey = Object.keys(input).find(k =>
  k.startsWith('__reactFiber') || k.startsWith('__reactInternals')
);

// 3. Navigate to the props containing onChange
const fiber = input[fiberKey];
// Sometimes props are on fiber directly, sometimes on fiber.memoizedProps
const props = fiber?.memoizedProps || fiber?.pendingProps;

// 4. Trigger React's onChange with a synthetic-compatible event object
props.onChange({ target: { value: 'hello@example.com' } });

// 5. For blur validation (if the field validates onBlur):
const onBlur = props.onBlur;
if (onBlur) onBlur({ target: input });
```

## Finding the Right Fiber Key

React's internal property name changes across versions:

| React version | Fiber key prefix |
|---|---|
| 16.x | `__reactInternalInstance$` |
| 17.x | `__reactFiber$` |
| 18.x | `__reactFiber$` |

Use the dynamic lookup to avoid version-pinning:
```javascript
const fiberKey = Object.keys(el).find(k =>
  k.startsWith('__reactFiber') || k.startsWith('__reactInternalInstance')
);
```

## Handling Select / Dropdown

```javascript
const select = document.querySelector('select[name="role"]');
const fiberKey = Object.keys(select).find(k => k.startsWith('__reactFiber'));
select[fiberKey].memoizedProps.onChange({ target: { value: 'admin' } });
```

## Handling Textarea

Same as input — `onChange` on the fiber props, pass `{ target: { value: '...' } }`.

## Triggering Submit After Fill

If filling inputs doesn't auto-submit, trigger the form's `onSubmit`:

```javascript
const form = document.querySelector('form');
const fiberKey = Object.keys(form).find(k => k.startsWith('__reactFiber'));
const props = form[fiberKey].memoizedProps;
props.onSubmit({ preventDefault: () => {} });
```

## Common Mistakes

| Mistake | Fix |
|---|---|
| `input.value = x` only | React state not updated; use fiber `onChange` |
| `input.dispatchEvent(new Event('input'))` | Native events bypass React's synthetic system |
| Accessing `fiber.props` | Use `fiber.memoizedProps`; `.props` may be stale |
| Not calling `onBlur` | Validation-on-blur fields won't show errors or enable submit |
| Assuming `__reactFiber$<hash>` suffix | Use dynamic key lookup; hash is build-specific |

## Diagnostic: Verify State Was Updated

```javascript
// Check that React's internal state reflects the new value
const inst = Object.keys(input).find(k => k.startsWith('__reactFiber'));
console.log(input[inst].memoizedProps.value);  // should be new value
```

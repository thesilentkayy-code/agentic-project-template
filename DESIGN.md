# Design & UI Guidelines (DESIGN.md)

## 1. Component Library
- We strictly use [shadcn/ui / Material / etc.] for UI components.
- Do not build custom components (e.g., Modals, Dropdowns) if a library equivalent exists.

## 2. Theming & Colors
- **NEVER use hardcoded colors** (e.g., `bg-red-500`, `text-black`).
- Use CSS variables to support light/dark mode (e.g., `bg-background`, `text-foreground`).

## 3. Typography
- Font Family: [Inter / Roboto / etc.]

## 4. User Experience (UX)
- All interactive icons MUST have tooltips.
- Destructive actions MUST have an AlertDialog confirmation.
- Never use browser-native `alert()`, `prompt()`, or `confirm()`. Use the app's toast system.

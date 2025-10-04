# AI Prompt Genius - Copilot Instructions

## Project Overview
AI Prompt Genius is a Chrome browser extension for curating custom AI prompt libraries. It's built with React + Vite as the UI framework, packaged as a Manifest v3 browser extension with Google Drive sync capabilities.

## Architecture & Key Components

### Dual Environment Setup
- **React App** (`src/`): Built with Vite, serves as the main UI hosted at `https://lib.aipromptgenius.app/`
- **Browser Extension** (`plugin/`): Manifest v3 extension that embeds the React app via iframe
- Extension popup, sidebar, and pages all load the hosted React app for consistent experience

### Data Flow & Storage
- **Primary Storage**: `localStorage` with `@uidotdev/usehooks` for reactive state
- **Data Structure**: Prompts have `id`, `title`, `content`, `tags`, `folder` properties
- **Cloud Sync**: Google Drive API integration (`src/components/js/cloudSyncing.js`) with OAuth2
- **Variable System**: Prompts support `{{variable}}` syntax for dynamic content replacement

### Extension-Specific Patterns
- **Message Passing**: Use `sendMessageToParent()` from `utils.js` for extension communication
- **Content Scripts**: Target ChatGPT specifically (`legacy_hotkey.js` for `https://chatgpt.com/*`)
- **Side Panel API**: Modern Chrome extension UI pattern (manifest `side_panel`)
- **Keyboard Shortcuts**: `Ctrl+Shift+U` (sidebar), `Ctrl+Shift+P` (search) via `commands` API

## Development Workflows

### Build Process
```bash
npm run build          # Vite build for React app
npm run css           # Tailwind compilation to src/App.css
./build_plugin.sh     # Package extension for distribution
```

### CSS Architecture
- **Tailwind + DaisyUI**: Pre-built component library with 13 theme options
- **Theme System**: React Context (`ThemeContext.jsx`) with localStorage persistence
- **Build Pattern**: Tailwind input → compiled → `src/App.css` (not typical Vite workflow)

### Internationalization
- **i18next**: 13 languages supported with browser extension message system
- **Dual i18n**: Extension manifest uses `__MSG_*__` format, React app uses i18next
- **Translation Files**: Both `src/i18n/translations/` and `plugin/_locales/` directories

## Key File Patterns

### Component Structure
- **Functional Components**: All React components use hooks, no class components
- **Local Storage Hooks**: Persistent state via `useLocalStorage("key", defaultValue)`
- **Modal Pattern**: Consistent modal implementation across settings, onboarding, transfers

### Extension Integration
- **iframe Communication**: `plugin/pages/forward-messages.js` handles message forwarding
- **Background Script**: Manages promotional campaigns with date-based logic
- **OAuth Flow**: Google Drive authentication handled in background script context

### Pro Feature System
- **Feature Gating**: `src/components/js/pro.js` handles premium functionality
- **Cloud Sync**: Pro feature for Google Sheets integration and backup
- **Analytics**: Google Analytics 4 integration for usage tracking

## Development Guidelines

### When Adding Features
- Use `useLocalStorage` for persistent state instead of manual localStorage calls
- Add i18n keys to both `src/i18n/translations/` and `plugin/_locales/` for consistency
- Test iframe communication between extension pages and React app
- Consider offline functionality - most features work without cloud sync

### Extension-Specific Considerations
- **Permissions**: Current permissions include `sidePanel`, `identity`, `storage`, `activeTab`, `scripting`
- **Content Security Policy**: iframe src must be HTTPS for security
- **Distribution**: Use `build_plugin.sh` to create Web Store compatible package

### Common Pitfalls
- Don't modify `src/App.css` directly - it's generated from `input.css` via Tailwind
- Extension manifest version must be updated manually (not automated)
- Google Drive API requires specific OAuth client configuration
- Test both popup and sidebar extension contexts - they have different dimensions

### Testing Approach
- Test extension in both popup (700x500px) and sidebar contexts
- Verify keyboard shortcuts work across different web pages
- Test offline functionality without cloud sync enabled
- Validate i18n switching affects both extension UI and React app
# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

### Core Commands
- `yarn install` - Install dependencies
- `yarn start` - Start local development server (http-server)
- `yarn test` - Run Jest unit tests
- `yarn lint` - Run ESLint with quiet mode
- `yarn build` - Generate build version file

### Pre-commit Hooks
The repository uses Husky pre-commit hooks that automatically run `yarn lint` before commits.

## Architecture Overview

Kittens Game is a JavaScript-based incremental game built with Dojo Toolkit (pre-1.7) and vanilla JavaScript. The codebase deliberately avoids modern ES6+ features to maintain compatibility with legacy browsers including IE6.

### Core Architecture
- **Dojo-based inheritance system**: All game components use `dojo.declare()` for class definitions
- **Tab Manager pattern**: Game sections (science, village, buildings, etc.) extend `com.nuclearunicorn.core.TabManager`
- **Effect system**: Centralized effect calculation and caching via metadata providers
- **Event-driven updates**: Timer-based game loop with scheduled events

### Key Files
- `core.js` - Base classes, UI controls, TabManager implementation (start here for codebase familiarity)
- `game.js` - Main game class, timer system, telemetry
- `config.js` - Game configuration (locales, themes, notations)
- `js/` directory - Game modules (buildings, science, religion, etc.)

### Code Style Requirements
- **No ES6**: No arrow functions, const/let, require, webpack, etc.
- **Legacy compatibility**: Must work on IE6 and ancient browsers
- **Dojo patterns**: Use `dojo.declare()`, `this.inherited(arguments)`, `dojo.connect()`
- **Constructor pattern**: Initialize arrays/objects in constructor, not as class properties

### Testing
- Jest configuration in `jest.config.unittest.json`
- Test files in `test/` directory
- Run specific tests with Jest CLI options

### Localization
- Master translations in `i18n/en.json`
- Use `$I(<string_id>)` instead of hardcoded strings
- Crowdin integration for community translations
- Auto-generated files in `i18n/crowdin/` should not be edited directly

### Themes
The game supports 20+ visual themes with corresponding CSS files in `res/` directory.
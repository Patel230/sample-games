# Contributing to Sample Games

Thank you for your interest in contributing to Sample Games! This document provides guidelines and instructions for contributing.

## How to Contribute

### Reporting Bugs

If you find a bug, please create an issue with:

1. A clear title
2. Steps to reproduce
3. Expected behavior
4. Actual behavior
5. Screenshots (if applicable)
6. Browser and OS information

### Suggesting Features

We welcome feature suggestions! Please create an issue with:

1. A clear title
2. Detailed description of the feature
3. Use case examples
4. Mockups (if applicable)

### Adding New Games

To add a new game to the collection:

1. Fork the repository
2. Edit `src/App.vue`
3. Add the game to the `GAMES` array following this format:

```typescript
{
  rank: number,
  name: "Game Name",
  dev: "Developer Name",
  cat: "Category",
  tagline: "Short description",
  desc: "Longer description",
  playUrl: "https://game-url.com",
  rating: "4.5* Rating",
  free: boolean,
  multi: boolean
}
```

4. Add a custom SVG icon to `GAME_ICONS` object
5. Submit a pull request

### Code Style

- Use TypeScript for all new code
- Follow Vue 3 Composition API patterns
- Use `<script setup>` syntax
- Add appropriate type annotations
- Keep components small and focused

### Development Setup

```bash
# Install dependencies
npm install

# Start dev server
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview
```

### Pull Request Process

1. Update the README.md with details of changes if applicable
2. Update the CHANGELOG.md with your changes
3. Make sure all tests pass (if applicable)
4. Request review from maintainers

## Code of Conduct

Please note that this project is released with a [Contributor Code of Conduct](CODE_OF_CONDUCT.md). By participating in this project you agree to abide by its terms.

## Questions?

Feel free to create an issue with your question and we'll do our best to help!

Thank you for contributing! 🎮

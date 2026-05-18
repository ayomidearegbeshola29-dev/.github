# Contributing to NexDAO

We love your input! We want to make contributing to NexDAO as easy and transparent as possible.

## Code of Conduct

This project and everyone participating in it is governed by our [Code of Conduct](CODE_OF_CONDUCT.md). By participating, you are expected to uphold this code.

## What We're Looking For

- **Bug fixes** — Clear, focused fixes for existing issues
- **New features** — Well-scoped features that align with the roadmap
- **Documentation** — Improvements to READMEs, API docs, and guides
- **Tests** — Test coverage for existing and new code
- **Issues** — Bug reports, feature requests, and questions

## How to Contribute

### 1. Find or Create an Issue

Start by browsing [open issues](https://github.com/ayomidearegbeshola29-dev/nexdao/issues). If you don't see what you're looking for, create a new one.

**Good first issues** are tagged with `good-first-issue`.

### 2. Fork and Clone

```bash
# Fork the repository on GitHub
# Then clone your fork:
git clone https://github.com/YOUR-USERNAME/nexdao-frontend.git
cd nexdao-frontend
git remote add upstream https://github.com/ayomidearegbeshola29-dev/nexdao-frontend.git
```

### 3. Create a Branch

```bash
git checkout -b feature/your-feature-name
```

Branch naming:
- `feature/` — New features
- `fix/` — Bug fixes
- `docs/` — Documentation changes
- `refactor/` — Code refactoring
- `test/` — Test additions

### 4. Make Changes

- Follow the existing code style
- Write or update tests for your changes
- Update documentation as needed
- Keep commits atomic and well-described

### 5. Commit

```bash
git commit -m "feat: add proposal creation form"
```

We follow [Conventional Commits](https://www.conventionalcommits.org/):
- `feat:` — New feature
- `fix:` — Bug fix
- `docs:` — Documentation
- `test:` — Tests
- `refactor:` — Code refactoring
- `chore:` — Build/config changes

### 6. Push and Open a PR

```bash
git push origin feature/your-feature-name
```

Then open a Pull Request on GitHub. Fill out the PR template with:
- What your PR does
- How to test it
- Screenshots (if UI changes)
- Related issues

### 7. Code Review

A maintainer will review your PR. They may request changes. Please respond to feedback promptly.

## Development Setup

### Frontend

```bash
cd nexdao-frontend
npm install
cp .env.example .env.local
npm run dev
```

### Backend

```bash
cd nexdao-backend
npm install
cp .env.example .env
# Set up PostgreSQL and update DATABASE_URL
npx prisma generate
npx prisma db push
npm run dev
```

## Testing

```bash
# Frontend
npm run lint

# Backend
npm test
npm run lint
```

## PR Review Criteria

Your PR will be reviewed for:

- **Correctness** — Does the code work as expected?
- **Code quality** — Is the code clean and maintainable?
- **Test coverage** — Are there tests for new code?
- **Documentation** — Are changes documented?
- **Performance** — Are there performance concerns?
- **Security** — Are there security implications?

## Questions?

Open a [Discussion](https://github.com/ayomidearegbeshola29-dev/nexdao/discussions) or reach out to maintainers.

---

Thank you for contributing to NexDAO! 🚀

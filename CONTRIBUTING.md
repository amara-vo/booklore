# Contributing to Booklore

🎉 **Thank you for your interest in contributing to Booklore!** Whether you're fixing bugs, adding features, improving documentation, or simply asking questions, every contribution helps make Booklore better for everyone.

Jump to:
[Getting Started](#-getting-started) • [Report a Bug](#-bugs--feature-requests) • [Community Support](#-community--support) • [PR Checklist](#-submitting-code-changes)

---

## 📚 What is Booklore?

**Booklore** is a modern, self-hostable digital library platform for managing and reading books and comics. It's designed with privacy, flexibility, and ease of use in mind.

**Tech Stack:**
- **Frontend**: Angular 20, TypeScript, PrimeNG 19
- **Backend**: Java 21, Spring Boot 3.5
- **Authentication**: Local JWT + optional OIDC (e.g., Authentik)
- **Database**: MariaDB
- **Deployment**: Docker-compatible, reverse proxy-ready

---

## 📦 Project Structure

```
booklore/
├── booklore-ui/           # Angular frontend application
├── booklore-api/          # Spring Boot backend API
├── assets/                # Shared assets (logos, icons, etc.)
├── docker-compose.yml     # Production Docker setup
└── dev.docker-compose.yml # Development Docker setup
```

---

## 🚀 Getting Started

### 1. Fork and Clone

First, fork the repository to your GitHub account, then clone it locally:

```bash
# Clone your fork
git clone https://github.com/<your-username>/booklore.git
cd booklore

# Add upstream remote to keep your fork in sync
git remote add upstream https://github.com/booklore-app/booklore.git
```

### 2. Keep Your Fork Updated

Before starting work on a new feature or fix, ensure your local `develop` branch is in sync with the upstream repository:

```bash
# Ensure you are on the develop branch
git checkout develop

# Fetch latest changes from the original Booklore repo
git fetch upstream

# Merge those changes into your local develop branch
git merge upstream/develop

# Push updates to your Github fork
git push origin develop
```

---

## 🧱 Local Development Setup

Booklore offers two development approaches: an all-in-one Docker stack for quick setup, or manual installation for more control.

### Option 1: Docker Development Stack (Recommended for Quick Start)

This option sets up everything with a single command:

```bash
docker compose -f dev.docker-compose.yml up
```

**What you get:**
- ✅ Frontend dev server at `http://localhost:4200/`
- ✅ Backend API at `http://localhost:8080/`
- ✅ MariaDB at `localhost:3366`
- ✅ Remote Java debugging at `localhost:5005`

**Note:** All ports are configurable via environment variables in `dev.docker-compose.yml`:
- `FRONTEND_PORT` (default: 4200)
- `BACKEND_PORT` (default: 8080)
- `DB_PORT` (default: 3366)
- `REMOTE_DEBUG_PORT` (default: 5005)

**Stopping the stack:**
```bash
docker compose -f dev.docker-compose.yml down
```

### Option 2: Manual Local Development

For more control over your development environment, you can run each component separately.

#### Prerequisites

Ensure you have the following installed:
- **Java 21+** ([Download](https://adoptium.net/))
- **Node.js 18+** and **npm** ([Download](https://nodejs.org/))
- **MariaDB 10.6+** ([Download](https://mariadb.org/download/))
- **Git** ([Download](https://git-scm.com/))

#### Frontend Setup

```bash
# Navigate to the frontend directory
cd booklore-ui

# Install dependencies
npm install

# Start the development server
ng serve

# Or use npm script
npm start
```

The frontend will be available at `http://localhost:4200/` with hot-reload enabled.

**Common Issues:**
- If you encounter dependency conflicts, try `npm install --legacy-peer-deps`
- Use `--force` only as a last resort

#### Backend Setup

##### Step 1: Configure Application Properties

Create a development configuration file at `booklore-api/src/main/resources/application-dev.yml`:

```yaml
app:
  # Path where books and comics are stored
  path-book: '/Users/yourname/booklore-data/books'
  
  # Path for thumbnails, metadata cache, and other config files
  path-config: '/Users/yourname/booklore-data/config'

spring:
  datasource:
    driver-class-name: org.mariadb.jdbc.Driver
    url: jdbc:mariadb://localhost:3306/booklore?createDatabaseIfNotExist=true
    username: root
    password: your_secure_password
```

**Important:**
- Replace `/Users/yourname/...` with actual paths on your system
- Create these directories if they don't exist
- Ensure proper read/write permissions

**Example paths:**
- **macOS/Linux**: `/Users/yourname/booklore-data/books`
- **Windows**: `C:\Users\yourname\booklore-data\books`

##### Step 2: Set Up the Database

Ensure MariaDB is running and create the database:

```bash
# Connect to MariaDB
mysql -u root -p

# Create database and user (optional)
CREATE DATABASE IF NOT EXISTS booklore;
CREATE USER 'booklore_user'@'localhost' IDENTIFIED BY 'your_secure_password';
GRANT ALL PRIVILEGES ON booklore.* TO 'booklore_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

##### Step 3: Run the Backend

```bash
cd booklore-api
./gradlew bootRun --args='--spring.profiles.active=dev'
```

The backend API will be available at `http://localhost:8080/`

**Verify it's running:**
```bash
curl http://localhost:8080/actuator/health
```

---

## 🔃 Development Workflow

To keep the project history clean and manageable, please follow this cycle:

1. **Create a branch** from `develop`
2. **Make your changes** in small, logical commits
3. **Test thoroughly** - run both frontend and backend tests
4. **Update documentation** if your changes affect usage
5. **Run the linter** and fix any issues
6. **Commit with clear messages** following Conventional Commits
7. **Push to your fork**
8. **Open a pull request** targeting the `develop` branch

---

## 🧪 Testing

Always run tests before submitting a pull request to ensure your changes don't break existing functionality.

### Frontend Tests (Angular + Vitest)

Booklore uses [Vitest](https://vitest.dev/) for fast, modern frontend testing in the Angular app.

```bash
cd booklore-ui

# Run all frontend tests
ng test

# Run tests with coverage report
ng test --coverage
```

- The coverage report will be generated in the `coverage/` directory.
- You can open `coverage/index.html` in your browser to view detailed coverage metrics.
- All new features and bug fixes should include relevant unit tests.

### Backend Tests

```bash
cd booklore-api

# Run all tests
./gradlew test

# Run tests with detailed output
./gradlew test --info

# Run a specific test class
./gradlew test --tests "com.booklore.api.service.BookServiceTest"

# Generate coverage report
./gradlew test jacocoTestReport
```

**Before creating a PR, always run:**
```bash
./gradlew test
```

---

## 🧼 Code Style & Conventions

- **Angular**: Follow the [official style guide](https://angular.io/guide/styleguide)
- **Java**: Use modern features (Java 21), clean structure
- **Linter**: Use IntelliJ IDEA's built-in linter for code formatting and style checks
- **UI**: Use SCSS and PrimeNG components consistently

### Branch Naming Convention

Create descriptive branches that clearly indicate the purpose of your changes:

```bash
# For new features
git checkout -b feat/add-dark-mode-theme
git checkout -b feat/epub-reader-support

# For bug fixes
git checkout -b fix/book-import-validation
git checkout -b fix/memory-leak-in-scanner

# For documentation
git checkout -b docs/update-installation-guide

# For refactoring
git checkout -b refactor/improve-authentication-flow
```

### Commit Message Format

We follow [Conventional Commits](https://www.conventionalcommits.org/) for clear, standardized commit messages.

#### Format

```
<type>(<scope>): <subject>

[optional body]

[optional footer]
```

#### Types

- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting, no logic change)
- `refactor`: Code refactoring
- `test`: Adding or updating tests
- `chore`: Maintenance tasks
- `perf`: Performance improvements

#### Examples

```bash
# Feature addition
feat(reader): add keyboard navigation for page turning

# Bug fix
fix(api): resolve memory leak in book scanning service

# Documentation
docs(readme): add troubleshooting section for Docker setup

# Multiple scopes
feat(api,ui): implement book collection management

# Breaking change
feat(auth)!: migrate to OAuth 2.1

BREAKING CHANGE: OAuth 2.0 is no longer supported
```

---


## 📤 Submitting Code Changes

To maintain the stability of Booklore, we enforce a **strict review process**. Follow these steps to ensure a smooth review process:

1. **Sync with `develop` branch:** Ensure your branch is up to date with the latest develop branch and resolve any conflicts.
2. **Run Local Tests:**
   - Backend: Run `./gradlew test`
   - Frontend: Run `ng test`
3. **Open a Pull Request:** Submit your PR against the `develop` branch.

### **Pull Request Mandatory Checklist**

When you open a PR, a template will automatically appear. Follow these ***mandatory requirements*** to avoid your PR being sent back:

- [ ] **Code adheres to project style guidelines and conventions**
- [ ] **Branch synchronized with latest `develop` branch** 
- [ ] **🚨 Automated unit tests added/updated to cover changes** _(for ALL Spring Boot backend and Angular frontend changes)_
- [ ] **🚨 All tests pass locally** _(run `./gradlew test` for Spring Boot backend, and `ng test` for Angular frontend)_
- [ ] **🚨 Manual testing completed in local development environment** _(verify your changes work AND no existing functionality is broken - test related features thoroughly)_
- [ ] **Flyway migration versioning follows correct sequence** _(if database schema was modified)_
- [ ] **Documentation PR submitted to [booklore-docs](https://github.com/booklore-app/booklore-docs)** _(required for features or enhancements that introduce user-facing or visual changes)_

---

## 💡 Bugs & Feature Requests

To help us stay organized, please follow these steps when reporting a bug or suggesting an improvement:

1. **Search issues:** Check **[ existing issues](https://github.com/booklore-app/booklore/issues)** to see if your topic has already been addressed.

2. **Select a template:** If you don't find a duplicate, **[open a new issue](https://github.com/booklore-app/booklore/issues/new/choose)** and choose the appropriate template:

- Bug Report – For reporting unexpected behavior or technical errors.

- Feature Request – For proposing new functionality or UI/UX improvements.

1. **Provide detail:** Fill out the template fields as completely as possible. For large features, we recommend discussing your idea in our [Discord](https://discord.gg/Ee5hd458Uz) first!

---

## 💬 Community & Support

**Need help or want to discuss ideas?**

- 💬 **Discord**: [Join our server](https://discord.gg/Ee5hd458Uz)
- 🐛 **Issues**: [GitHub Issues](https://github.com/booklore-app/booklore/issues)


**Code of Conduct**

This project and everyone participating in it is governed by our [Code of Conduct](.github/CODE_OF_CONDUCT.md). By contributing, you are expected to uphold this code.

---

## 📄 License

Booklore is open-source software licensed under the **GPL-3.0 License**.

By contributing, you agree that your contributions will be licensed under the same license. See the [`LICENSE`](./LICENSE) file for full details.

---

## 🎯 What to Work On?

Not sure where to start? Check out:

- Issues labeled [`good first issue`](https://github.com/booklore-app/booklore/labels/good%20first%20issue)
- Issues labeled [`help wanted`](https://github.com/booklore-app/booklore/labels/help%20wanted)
- Our [project roadmap](https://github.com/booklore-app/booklore/projects)

---

## 🎉 Thank You!

Every contribution, no matter how small, makes Booklore better. Thank you for being part of our community!

**Happy Contributing! 📚✨**

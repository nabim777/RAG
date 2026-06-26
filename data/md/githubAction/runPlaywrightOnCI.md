---
title: Playwright and GitHub Actions - Run tests in CI
authorName: Nabin Ale
authorAvatar : https://avatars.githubusercontent.com/u/61624650?v=4
authorLink: https://github.com/nabim777
createdAt: Apr 8, 2025
tags: continuous integration, continuous delivery, continuous deployment, github actions, ci, cd, playwright
banner: https://blog.jankaritech.com/src/assets/RunPlaywrightOnCI/images/githubaction_with_playwright_banner
---

This is a blog about how to run Playwright UI tests in GitHub Actions.
Before you start, you need to have basic knowledge of **GitHub Actions**, **Playwrigh**t and the **Playwright Trace Viewer** to better understanding this blog. If you are not familiar with any of these, don't worry! below are links to our blogs that explain each topioc in a simple way:
1. [Introduction to GitHub Actions](https://blog.jankaritech.com/#/blog/Introduction%20to%20GitHub%20Actions%20-%20CI%20%26%20CD)
2. [Playwright](https://blog.jankaritech.com/#/blog/E2E%20Testing%20using%20BDD%20with%20Playwright/Behavior%20Driven%20Development%20(BDD)%20using%20Playwright)
3. [Debugging and Error Tracing in Playwright](https://blog.jankaritech.com/#/blog/E2E%20Testing%20using%20BDD%20with%20Playwright/Debugging%20and%20Error%20Tracing%20in%20Playwright)

By the end of this blog, you will be able to set up a CI workflow that automatically runs your Playwright UI tests whenever you push code to your repository. You will also learn how to get Playwright trace reports when tests fail, making it easier to debug and fix errors.

## 🤔 Why to run tests on CI?

Tests are run on CI(continuous integration) to ensure the code works properly in a clean and isolated environment every time a change is made. Here are some reasons:

**1. Early bug detection:**
CI runs tests automatically on every push or PR, so you find problems right when they’re introduced, not days or weeks later when the context is gone and fixes are harder.

**2. No more “Works on my machine”**
CI runs your tests in a clean environment. If the tests pass there, it means your code works for everyone, not just on your computer.

**3. Protect the main branch**
Tests on CI act like a gatekeeper. If something breaks, it doesn’t get merged. This keeps main (or master) deployable and sane.

**4. Confidence to refactor and move fast**
When tests run automatically, you can refactor aggressively. If CI is green, you didn’t break existing behavior. That confidence speeds teams up a lot.

**3. Improved code quality**
Every change is automatically tested in CI. Problems are caught early, so the code stays stable, clean, and reliable.

**5. Better collaboration**
CI gives everyone on the team the same test results. This helps avoid confusion and makes it clear whether the code is working or not.

**6. Automated process:**
CI systems enable developers to save time and effort testing things.


## 📘 About the Project

![Login page of the Project](/src/assets/githubAction/images/project_login_page.png "momo application")

In this blog, I have taken simple applications built using Vue.js. The GitHub repository is available at: https://github.com/nabim777/momo-restro-list.git

This is a basic application that includes login and logout functionality. E2E test is written using Playwright to verify the login feature.

## 🛠️ Running App Locally
Before setting up CI, let's run the application and tests locally.

**1.  Install dependencies**

```bash
npm install
```

**2. Start the frontend and backend servers**


```bash
npm run serve              # Start frontend
npm run backend            # Start backend
```

## 🧪 Running UI Tests Locally

> **_NOTE:_** Make sure both the frontend and backend are running before running the tests.

To run the UI tests locally, use the following command:

```bash
npm run test:e2e tests
```

## ⚙️ Setting Up CI in GitHub Actions
After verifying the app locally, the next step is to set up CI using GitHub Actions. First, create a file named `ci.yml` in a project using the following folder structure:

```
📦momo-restro-list
┗ 📂.github
┃ ┣ 📂workflows
┃ ┃ ┗ 📜ci.yml
```

Then, add the following code in the `ci.yml`.

```yml
name: Run-project

on:
  push:
    branches:
      - master
  pull_request:
    branches:
      - master
  workflow_dispatch:

jobs:
  run-Restro-project:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repo code
        uses: actions/checkout@v3

      - name: Set up node
        uses: actions/setup-node@v3
        with:
          node-version: 20.x

      - name: Install dependencies
        run: |
          npm ci
          npx playwright install chromium

      - name: JS lint
        run: |
          npm run lint || (echo "Linting failed! Please run 'npm run lint:fix' to fix the errors." && exit 1)

      - name: Run the project
        run: |
          npm run serve &
          npm run backend &

      - name: Wait for services
        run: |
          sudo apt-get install wait-for-it -y
          wait-for-it -h localhost -p 8080 -t 10
          wait-for-it -h localhost -p 3000 -t 10

      - name: Run ui tests
        id: test-ui
        run: npm run test:e2e tests

      - name: Upload trace results
        if: ${{ failure() && steps.test-ui.conclusion == 'failure' }}
        uses: actions/upload-artifact@v4
        with:
          path: |
            trace-results/*.zip
            retention-days: 30
```


## 🔍 What this Workflow Does?
This GitHub Actions file runs when you push to the `master` branch or create a pull request to `master`

It has one job called `run-Restro-project` with these steps:
1. **Checkout repo code** - Gets the project code from GitHub.
2. **Set up node** - Installs Node.js version 20.
3. **Install dependencies** - Installs the project dependencies and Playwright browsers.
4. **JS lint** - Runs the linter to check for code quality issues. If linting fails, it will print an custom error message and exit with a failure status.
5. **Run the project** - Starts the Vue app, and starts the backend using json-server.
6. **Wait for services** - Waits for the frontend (port 8080) and backend (port 3000) to be ready.
7. **Run ui tests** - Installs Playwright and runs the UI tests.
8. **Upload trace results** - If the UI tests fail, it uploads the Playwright trace results as an artifact that can be downloaded from the GitHub Actions interface. The trace files will be retained for 30 days.

## 📝 Conclusion
Using GitHub Actions to run your Playwright tests means your app is tested automatically in a clean environment every time you make changes. It helps find bugs early, makes teamwork easier, and keeps your project stable.

> CI tests turn "hope it works" into "we know it works."

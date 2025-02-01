# GitHub Actions Workflow Breakdown

This GitHub Actions workflow automates the deployment of a React.js project to **Surge** whenever there is a push to the ``main`` branch.


## 1️⃣  Workflow Name

```yml
name: Deploy to Surge

```
- This names the workflow "**Deploy to Surge**".
- You’ll see this name in the **Actions** tab in your GitHub repository.

## 2️⃣ Triggering the Workflow

```yml
on:
  push:
    branches: [ main ]

```

- This means the workflow runs **only when you push changes** to the ``main`` branch.
- If you push to any other branch, the workflow **will not** trigger.

## 3️⃣ Jobs Definition

```yml
jobs:
  build:
    runs-on: ubuntu-latest

```

- **`jobs:`** Defines the tasks that will be executed.
- **`build:`** The name of the job (can be anything).
- **`runs-on:`** ubuntu-latest: Runs the job on a Linux-based GitHub-hosted runner.


## 4️⃣ Steps in the Job

Each job consists of multiple **steps** that execute in order.

### Step 1: Checkout Repository

```yml
    - name: Checkout Repository
      uses: actions/checkout@v4

```

- This **downloads** (clones) the GitHub repository inside the runner.
- It allows GitHub Actions to access the project files.


### Step 2: Setup Node.js


```yml
    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: 18

```

- **Sets up Node.js v18** as the environment for running the project.
- ``uses: actions/setup-node@v4`` is an official GitHub Action for setting up Node.js.
- `with: node-version: 18` ensures the workflow runs with Node.js 18.



### Step 3: Install Dependencies

```yml
    - name: Install Dependencies
      run: yarn install
```

- Installs all project dependencies using **Yarn** (like ``npm install``).
- Uses the ``package.json`` file to install the required packages.


### Step 4: Build the React Project

```yml
    - name: Build Project
      run: yarn build
```


- Runs `yarn build` to compile the React.js project.
- This generates a `build/` folder containing the optimized production files


### Step 5: Install Surge

```yml
    - name: Install Surge
      run: npm install --global surge

```

- Installs **Surge** globally using `npm`.
- Surge is a tool that helps deploy static websites for free.



### Step 6: Deploy to Surge


```yml
    - name: Deploy to Surge
      run: surge ./build https://shoes_store1.surge.sh/ --token ${{ secrets.SURGE_TOKEN }}
```
- Deploys the React.js project to Surge using the command:

```sh
surge ./build https://shoes_store1.surge.sh/ --token ${{ secrets.SURGE_TOKEN }}
```

- Explanation:

- `./build` → The folder containing the production files.
- `https://shoes_store1.surge.sh/` → The custom Surge domain where the project is deployed.
- `${{ secrets.SURGE_TOKEN }}` → Uses a **GitHub Secret** (`SURGE_TOKEN`) to authenticate with Surge.
- You **must set the Surge token in GitHub Secrets**(under Settings → Secrets and variables).


# Final Workflow Summary

 When you push to ``main``, GitHub Actions:

#### ✅ Checks out the repo.
#### ✅ Sets up Node.js 18.
#### ✅ Installs dependencies using Yarn.
#### ✅ Builds the React.js project.
#### ✅ Installs Surge.
#### ✅ Deploys the build to https://shoes_store1.surge.sh/ using Surge Token.




# My Daily Development Workflow (Git + Docker + CI/CD + Forking)

This guide outlines the complete process from starting a new task to seeing it live on the production website. Forking lets you keep your work private (until you’re ready) and is the standard for contributing to public repos. You’ll work from your own copy, then PR into the main project.

---

### 0. One-Time Setup: Fork the Repo (Initializing Your Local Environment)

This section covers two common starting points: cloning your fork for the first time, or adapting an existing local clone of the original repository to work with your fork.

- Go to the original repo on GitHub and click **Fork**.

**Option A: You're starting fresh (no existing local copy of the repo)**

If you don't have a local copy of the repository yet, follow these steps:

1.  **Clone your fork to your machine:**
    ```bash
    git clone [https://github.com/](https://github.com/)<your-username>/<repo-name>.git
    cd <repo-name>
    ```
    This creates a new folder named `<repo-name>` on your machine, which is a clone of your personal fork.

2.  **Add the original repo as an "upstream" remote:**
    ```bash
    git remote add upstream [https://github.com/](https://github.com/)<original-owner>/<repo-name>.git
    ```

**Option B: You already have a local copy of the original repo**

If you previously cloned the *original* repository (not your fork) and now want to use the forking workflow, follow these steps to reconfigure your existing local copy:

1.  **Navigate to your existing local repository folder:**
    ```bash
    cd /path/to/your/existing-repo-folder
    ```

2.  **Verify your current remotes:**
    ```bash
    git remote -v
    ```
    You will likely see `origin` pointing to the original repository. We need to change this.

3.  **Change your `origin` remote to point to your fork:**
    ```bash
    git remote set-url origin [https://github.com/](https://github.com/)<your-username>/<repo-name>.git
    ```
    This reconfigures your `origin` remote to be your personal fork.

4.  **Add the original repo as an "upstream" remote:**
    ```bash
    git remote add upstream [https://github.com/](https://github.com/)<original-owner>/<repo-name>.git
    ```
    This creates a new remote named `upstream` that points to the original project.

---

**After either Option A or Option B, you should have:**

- `origin` → your fork (where you push your work)
- `upstream` → the main/original repo (where you pull updates)

You can verify this with:
```bash
git remote -v
```
You should see entries for both `origin` (your fork's URL) and `upstream` (the original repo's URL).

---

### 1. Starting a New Feature or Fix

Before writing any code, prepare your local environment and branch.

* **Step 1.1: Sync Your Fork’s `main` with Upstream**
    Always start with the latest version of the project. This is the most important step to prevent future merge conflicts.
    ```bash
    # Fetch latest changes from the original/upstream repo
    git fetch upstream

    # Switch to your main branch
    git checkout main

    # Merge upstream changes into your fork's main
    git merge upstream/main

    # Push the updated main branch to your fork on GitHub
    git push origin main
    ```

* **Step 1.2: Create a New Branch**
    Create a descriptive branch for your task. Never work directly on `main`. This isolates your work.
    ```bash
    git checkout -b <type/your-branch-name>
    ```

---

### 2. The Local Development Loop (Code & Test)

This is where you'll spend most of your time. You will use Docker to run a live, local version of your entire Firebase environment.

* **Step 2.1: Start the Local Environment**
    ```bash
    docker-compose up --build
    ```
    Wait for the message: `✔ All emulators ready!`

* **Step 2.2: Code and See Instant Changes**
    - Edit files in your project.
    - Save and refresh your browser at **http://localhost:5000** to see changes.

* **Step 2.3: Stop the Environment**
    ```bash
    # In the terminal running Docker Compose:
    Ctrl+C
    docker-compose down
    ```

---

### 3. Saving and Sharing Your Progress (Git)

Once you've completed a logical chunk of work, save it to your Git repository.

* **Step 3.1: Stage and Commit Your Work**
    ```bash
    git add .
    git commit -m "feat: Add user profile page foundation"
    ```

* **Step 3.2: Push Your Branch to Your Fork**
    ```bash
    git push origin <your-branch-name>
    ```

---

### 4. Code Review and Automated Preview (GitHub)

Now you move from your local machine to the GitHub website.

* **Step 4.1: Open a Pull Request from Your Fork**
    - Go to your fork on GitHub.
    - Click "Compare & pull request" to propose your changes to the original repo.
    - Give it a clear title and a detailed description.

* **Step 4.2: Wait for Checks and Review the Preview**
    - CI/CD will run.
    - The `firebase-hosting-bot` will post a preview URL.
    - GitHub will check for merge conflicts automatically. If it shows `This branch has conflicts that must be resolved`, proceed to Step 5.
    - If you need to make changes based on feedback, repeat the local dev loop, commit, and push. The PR updates automatically.

---

### 5. Handling Merge Conflicts (When They Happen)

This happens when someone else's changes were merged into `main` *after* you created your branch. This same process should be used proactively to keep a long-running branch up to date.

* **Step 5.1: Make a Safety Commit**
    Before starting the merge, commit any work-in-progress to ensure nothing is lost.
    ```bash
    git add .
    git commit -m "wip: saving before merge"
    ```

* **Step 5.2: Update Your Local Repo with the Latest `main`**
    Make sure you are in your feature branch locally (`git checkout <your-branch-name>`), then run:
    ```bash
    # Fetch the very latest changes from the original/upstream repo
    git fetch upstream
    ```

* **Step 5.3: Merge the Latest `main` into Your Feature Branch**
    This will download the latest code and attempt to merge it into your branch.
    ```bash
    git merge upstream/main
    ```
    Your terminal will now likely show an error and list the conflicted files.

* **Step 5.4: Resolve Conflicts Locally**
    - Open the project in your code editor. Open each file marked with a conflict.
    - Manually edit the files to remove the conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`) and decide what the final, correct version of the code should be.

    <details>
    <summary><i>Click here for a detailed guide using VS Code</i></summary>

    - Your conflicting files will be highlighted in the Source Control panel.
    - When you open a conflicted file, VS Code provides inline helper buttons over each conflict block:
      - **`Accept Current Change`**: Keeps only your changes.
      - **`Accept Incoming Change`**: Keeps only the changes from `main`.
      - **`Accept Both Changes`**: Keeps both sets of changes, one after the other.
    - Choose the appropriate action for each conflict. You can still manually edit the code after clicking a button to get it perfect.
    - Once you have resolved a conflict block, the highlighting will disappear. Save the file.
    </details>

* **Step 5.5: Mark Conflicts as Resolved by Staging Files**
    After resolving all conflicts in a file and saving it, you must tell Git it's resolved by staging the file.
    ```bash
    git add .
    ```
    <details>
    <summary><i>How Staging Works in the VS Code UI</i></summary>

    - **Merge Changes**: In the VS Code Source Control panel, you'll see a "Merge Changes" section. These are the files with active conflicts that must be resolved.
    - **Staging**: After you fix a file and `git add` it (or use the "+" icon in the UI), it moves from "Merge Changes" to "Staged Changes". This is how you tell Git a conflict in that file is officially resolved.
    - You cannot finalize the merge until all conflicted files are moved to "Staged Changes".
    </details>

* **Step 5.6: Finalize the Merge with a Commit**
    Once all conflicts are resolved and staged, run the `commit` command *without* a message.
    ```bash
    git commit
    ```
    - This will open a command-line text editor (often Vim) with a pre-populated, default merge commit message.
    - To save and quit in Vim, press `Esc`, then type `:wq`, and finally press `Enter`.
    - You do not need to change the message. Using the default is the correct procedure for finalizing a merge and avoids the `fatal: cannot do a partial commit during a merge` error.

* **Step 5.7: Push the Fix to Your PR**
    ```bash
    git push origin <your-branch-name>
    ```
    The Pull Request on GitHub will automatically update, and the conflict warning should disappear.

---

### 6. Merging and Deploying to Production

* **Step 6.1: Squash and Merge (done by repo maintainers)**
    - After approval and all checks pass, your PR is merged into the main repo.

* **Step 6.2: Watch the Final Deployment**
    - The main repo’s CI/CD deploys your changes.

* **Step 6.3: Clean Up**
    - Delete your feature branch from your fork (and locally, if you want).

---

### Notes

#### General
- **Sync your fork regularly:** Even when you aren't working on a feature, it's good to periodically repeat Step 1.1 to keep your fork's `main` branch from getting stale.
- **You can have as many feature branches as you want in your fork.**
- **Your work stays private until you open a PR.**

#### Best Practices to Prevent Conflicts
- **Sync First, Always:** Never start new work without first completing Step 1.1.
- **Use Short-Lived Branches:** The longer a branch exists without merging, the more it diverges from `main`, increasing the chance of major conflicts. Aim for small, focused PRs.
- **Sync Your Feature Branch Periodically:** Don't let your feature branch get too out of sync from the `main` branch. To update it, follow the same process outlined in Step 5 (`git fetch upstream`, `git merge upstream/main`). Good times to sync are:
    - **On a schedule:** For branches that live longer than a day, sync with `main` every day or two to handle integrations in small chunks.
    - **After a major change:** If you know a teammate has merged a large feature that might affect your work, sync as soon as possible.
    - **Before creating a pull request:** As a final check, sync your branch one last time right before you create your PR. This lets you handle any last-minute conflicts privately, ensuring a clean pull request.
- **Communicate:** If you are working on a large feature that might conflict with others' work, talk to your teammates.

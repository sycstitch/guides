# My Fork Development Workflow (Git + Docker + CI/CD + Forking)

This guide outlines the complete process from starting a new task to seeing it live on the production website for projects where you contribute via a fork. Forking lets you keep your work private (until you’re ready) and is the standard for contributing to public repos or projects where you don't have direct write access to the main branch. You’ll work from your own copy (your fork), then propose your changes via a Pull Request into the main project.

---

### 0. One-Time Setup: Fork the Repo (Initializing Your Local Environment)

This section covers two common starting points: cloning your fork for the first time, or adapting an existing local clone of the original repository to work with your fork.

- Go to the original repo on GitHub and click **Fork**. This creates a copy of the repository under your GitHub account.

**Option A: You're starting fresh (no existing local copy of the repo)**

If you don't have a local copy of the repository yet, follow these steps:

1.  **Clone your fork to your machine:**
    ```bash
    git clone [https://github.com/](https://github.com/)<your-username>/<repo-name>.git
    cd <repo-name>
    ```
    This creates a new folder named `<repo-name>` on your machine, which is a clone of your personal fork. Git will automatically set this as your `origin` remote.

2.  **Add the original repo as an "upstream" remote:**
    ```bash
    git remote add upstream [https://github.com/](https://github.com/)<original-owner>/<repo-name>.git
    ```
    This command adds a new remote named `upstream` that points to the original (source) repository. You'll pull updates from here.

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
    You will likely see `origin` pointing to the original repository. For the forking workflow, we need to change this so `origin` points to *your fork*.

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

- `origin` → your fork (where you push your work and where your Pull Requests will originate from)
- `upstream` → the main/original repo (where you pull updates to keep your fork in sync)

You can verify this with:
```bash
git remote -v
```
You should see entries for both `origin` (your fork's URL) and `upstream` (the original repo's URL).

---

### 1. Starting a New Feature or Fix

Before writing any code, prepare your local environment and branch.

* **Step 1.1: Sync Your Fork’s `main` with Upstream**
    Always start with the latest version of the project. This is the most important step to prevent future merge conflicts by ensuring your fork's `main` branch is up-to-date with the original repository.

    **Understanding `git fetch` vs. `git pull`:**
    While `git pull` is a common command often used to update a local branch, it's actually a shorthand that combines `git fetch` (downloading remote changes to your local Git repository without modifying your working files) and `git merge` (integrating those changes into your current branch). We separate these steps in this workflow for greater clarity and control, allowing you to review what changes are available on the remote before integrating them into your local branch.

    ```bash
    # Fetch latest changes from the original/upstream repo
    git fetch upstream

    # Switch to your main branch
    git checkout main

    # Merge upstream changes into your fork's main
    # This integrates the latest changes from the original project into your local main branch.
    git merge upstream/main

    # Push the updated main branch to your fork on GitHub
    # This keeps your fork's 'main' branch in sync with the original 'main' branch on GitHub.
    git push origin main
    ```

* **Step 1.2: Create a New Branch**
    Create a descriptive branch for your task. Never work directly on `main`. This isolates your work and prepares it for a Pull Request.
    ```bash
    git checkout -b <type/your-branch-name>
    ```
    * **Tip for branch names:** Use prefixes like `feat/` for new features, `fix/` for bug fixes, `chore/` for maintenance, `docs/` for documentation updates, etc. (e.g., `feat/user-profile-page`, `fix/login-bug`).

---

### 2. The Local Development Loop (Code & Test)

This is where you'll spend most of your time, developing and testing your changes using your local Docker environment.

* **Step 2.1: Start the Local Environment**
    Before you start coding, spin up your local development environment using Docker Compose.
    ```bash
    docker-compose up --build
    ```
    Wait for the message: `✔ All emulators ready!` indicating that your services are running.

* **Step 2.2: Code and See Instant Changes**
    - Edit files in your project's codebase using your preferred code editor.
    - Save your changes and refresh your browser (typically at **http://localhost:5000**) to see your updates reflected instantly in the running Docker environment.

* **Step 2.3: Stop the Environment**
    When you're done with a coding session or need to restart your services, stop the Docker environment gracefully.
    ```bash
    # In the terminal running Docker Compose:
    Ctrl+C
    docker-compose down
    ```
    This command stops and removes the containers, networks, and volumes created by `docker-compose up`.

---

### 3. Saving and Sharing Your Progress (Git)

Once you've completed a logical chunk of work (even if the feature isn't finished), save it to your Git repository and push it to your fork on GitHub for backup.

* **Step 3.1: Stage and Commit Your Work**
    First, stage your changes to include them in the next commit, then create a commit with a descriptive message. Commit messages should briefly summarize *what* changed and *why*.
    ```bash
    git add .
    git commit -m "feat: Add user profile page foundation"
    ```
    * **Tip:** Commit frequently! Small, focused commits are easier to track, revert, and understand.

* **Step 3.2: Push Your Branch to Your Fork**
    After committing locally, push your branch to your remote fork on GitHub. This acts as your offsite backup, protecting your work from local machine failures, and makes your branch available to create a Pull Request from.
    ```bash
    git push origin <your-branch-name>
    ```
    * The first time you push a new branch, Git might suggest using `--set-upstream origin <your-branch-name>` to link your local branch to the remote one, simplifying future pushes.

---

### 4. Code Review and Automated Preview (GitHub)

Now you move from your local machine to the GitHub website to propose your changes to the original project.

* **Step 4.1: Open a Pull Request from Your Fork (as a Draft)**
    Go to your fork on GitHub. You'll likely see a prominent banner or prompt indicating that your recently pushed branch has new commits and suggesting you "Compare & pull request" with the original repository.
    * Click this prompt.
    * Fill out a clear title and a detailed description for your proposed changes.
    * **Crucially, mark it as a "Draft Pull Request"** (usually a dropdown option next to the "Create pull request" button).
    * **Why a Draft PR?** A Draft PR clearly signals that your work is still in progress, not ready for review or merging into the main project. It allows any configured CI/CD checks (like tests, linters, deployment previews) to run on the main repository, giving you instant feedback without implying the code is complete. This is how you keep your ongoing work "mostly private" until you're ready to submit it for formal review.

* **Step 4.2: Wait for Checks and Review the Preview**
    Once your Draft PR is open, any automated CI/CD workflows configured in the *original repository* will run against your branch (as part of the PR checks).
    * Monitor these checks for success or failure.
    * If the CI/CD includes a deployment preview, a bot (like `firebase-hosting-bot`) will post a preview URL in the PR comments. Use this to review your changes in a live environment.
    * GitHub will also check for merge conflicts automatically against the target branch (usually `main` in the original repo). If it shows `This branch has conflicts that must be resolved`, proceed to Step 5.
    * If you need to make more changes based on feedback from checks or further testing, repeat the local development loop (Step 2), commit, and push (Step 3). Your Draft PR on GitHub will automatically update with these new commits.

---

### 5. Handling Merge Conflicts (When They Happen)

This happens when someone else's changes were merged into the *original repository's `main` branch* after you created your feature branch. This same process should be used proactively to keep a long-running feature branch up to date with the original project's `main`.

* **Step 5.1: Make a Safety Commit**
    Before attempting to merge new changes from `upstream/main`, commit any work-in-progress on your feature branch to ensure nothing is lost if the merge process goes awry.
    ```bash
    git add .
    git commit -m "wip: saving before merge"
    ```

* **Step 5.2: Update Your Local Repo with the Latest `main` from Upstream**
    Make sure you are on your feature branch locally (`git checkout <your-branch-name>`), then fetch the latest changes from the original/upstream repository.
    ```bash
    # Fetch the very latest changes from the original/upstream repo
    git fetch upstream
    ```

* **Step 5.3: Merge the Latest `main` into Your Feature Branch**
    This command will attempt to integrate the latest code from `upstream/main` into your current feature branch. If there are conflicting changes, Git will pause and notify you.
    ```bash
    git merge upstream/main
    ```
    Your terminal will now likely show an error and list the conflicted files.

* **Step 5.4: Resolve Conflicts Locally**
    - Open your project in your code editor. Git will mark conflicting files with special markers (`<<<<<<<`, `=======`, `>>>>>>>`).
    - Manually edit each conflicted file to remove these markers and decide what the final, correct version of the code should be. You'll choose to keep your changes, the incoming changes from `upstream/main`, or a combination of both.

    <details>
    <summary><i>Click here for a detailed guide using VS Code's built-in conflict resolution</i></summary>

    - Your conflicting files will be highlighted in the Source Control panel.
    - When you open a conflicted file, VS Code provides inline helper buttons over each conflict block:
        - **`Accept Current Change`**: Keeps only your changes.
        - **`Accept Incoming Change`**: Keeps only the changes from `main`.
        - **`Accept Both Changes`**: Keeps both sets of changes, one after the other.
    - Choose the appropriate action for each conflict. You can still manually edit the code after clicking a button to fine-tune it.
    - Once you have resolved a conflict block, its highlighting will disappear. Save the file.
    </details>

* **Step 5.5: Mark Conflicts as Resolved by Staging Files**
    After resolving all conflicts in a file and saving it, you must tell Git that the file is resolved by staging it.
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
    Once all conflicts are resolved and staged, run the `commit` command *without* a message. Git will automatically generate a default merge commit message for you.
    ```bash
    git commit
    ```
    - This will typically open a command-line text editor (like Vim or Nano) with a pre-populated merge commit message.
    - To save and quit in Vim, press `Esc`, then type `:wq`, and finally press `Enter`. In Nano, press `Ctrl+X`, then `Y` to save, then `Enter`.
    - You usually do not need to change this default merge message. Using the default is the correct procedure for finalizing a merge.

* **Step 5.7: Push the Fix to Your PR**
    After successfully merging `upstream/main` into your feature branch and committing the merge, push these changes to your remote branch on your fork.
    ```bash
    git push origin <your-branch-name>
    ```
    The Pull Request on GitHub will automatically update, and the conflict warning should disappear.

---

### 6. Merging and Deploying to Production

Once your contribution is complete, thoroughly tested, and passes all checks, it's ready to be merged into the original project's `main` branch.

* **Step 6.1: Mark Pull Request as Ready & Await Merge**
    - On your Pull Request page on GitHub, if it's a Draft, first click "Ready for review."
    - **The repository maintainers** will review your Pull Request. After approval and all checks pass, they will merge your PR into the original project's `main` branch. This step is performed by the maintainers of the original repository, not by you.
    - Common merge strategies they might use include "Squash and Merge," "Rebase and Merge," or a regular "Merge commit."

* **Step 6.2: Watch the Final Deployment**
    Once your PR is merged into the original repository's `main` branch, their CI/CD pipeline will typically deploy the updated `main` to production.

* **Step 6.3: Clean Up Your Branches**
    Once your feature branch has been successfully merged into the original project's `main` branch, it's good practice to clean up both the remote branch on your fork and your local copy. This keeps your fork tidy and your branch list manageable.

    **Explanation:** When your Pull Request is merged, the original repository usually provides an option to delete the branch from *your fork* (e.g., via a "Delete branch" button on the merged PR page). However, your local Git repository still has its own copy of the branch, and it also retains "remote-tracking branches" (local references like `origin/your-branch-name`) that point to where the branch *used to be* on your fork. We need to clean up both of these.

    1.  **Switch to `main` (or another active branch) locally:**
        You cannot delete a branch that you are currently on. Before deleting your feature branch, switch to your `main` branch (or any other branch you plan to work on next).

        ```bash
        git checkout main
        ```

    2.  **Delete the Local Branch:**
        This removes the copy of the feature branch from your local machine.

        ```bash
        git branch -d <your-branch-name>
        ```
        * Use `-d` (lowercase) for a "safe" delete. Git will only delete the branch if it has been fully merged into its upstream (e.g., your local `main` branch, which should have been synced with `upstream/main`).
        * **Troubleshooting `error: the branch 'branch-name' is not fully merged`:** This often happens if your local `main` branch isn't fully up-to-date with the merge that happened in the *original* repository, or if the original repository's maintainers used a "Squash and Merge" / "Rebase and Merge" strategy (which creates new commits on `main` rather than directly incorporating the old ones).
            * If you are **certain** your changes from this branch are now in the original project's `main` on GitHub, you can force the local deletion using `-D` (uppercase):
                ```bash
                git branch -D <your-branch-name>
                ```
            * *(Optional: If you haven't recently, `git pull origin main` or even better, `git fetch upstream` then `git merge upstream/main` into your local `main` first, might help `git branch -d` succeed by updating your local `main`'s history.)*

        * **To delete multiple merged local branches at once (use with extreme caution!):**
            ```bash
            git branch --merged | grep -v "main" | xargs git branch -d
            ```
            This command lists all branches that have been merged into your current branch (likely `main`), excludes `main` itself, and then deletes the rest. Always double-check the output of `git branch --merged` *before* piping to `xargs` if you're unsure.

    3.  **Delete the Remote Branch on Your Fork (if not already done via GitHub UI):**
        If you didn't delete the branch from your fork on GitHub (e.g., via the PR interface after the original repo merged your PR), you can delete it from your remote fork using the command line.

        ```bash
        git push origin --delete <your-branch-name>
        ```
        *An alternative, older syntax for the same command is: `git push origin :<your-branch-name>`*

    4.  **Prune Stale Remote-Tracking Branches (to clean up your local Git client's view):**
        Even after deleting the remote branch from your fork and your local branch, your local Git repository might still retain a reference to the remote branch (e.g., `origin/feat/git-branch-names`). These are called "remote-tracking branches" and they won't automatically disappear. Pruning removes these stale references.

        ```bash
        git fetch --prune
        ```
        * This command fetches any new changes from the remote (`origin`) and simultaneously removes any remote-tracking branches that no longer exist on the `origin` remote.
        * After running this, your local Git client's branch view (and `git branch -r` output) should reflect the actual state of your remote branches, with the deleted one gone.

---

### Notes

#### General Best Practices
- **Sync Your Fork Regularly:** Even when you aren't working on a feature, it's good practice to periodically repeat Step 1.1 to keep your fork's `main` branch up-to-date with the original repository. This significantly reduces the chances of large merge conflicts in your feature branches later.
- **You Can Have as Many Feature Branches as You Want in Your Fork:** Don't hesitate to create new branches for every distinct task or idea.
- **Work Stays Private (until ready):** Your work is isolated on its branch within your fork. By consistently pushing your commits to your fork on GitHub (Step 3.2), you ensure your work is backed up offsite. Using Draft Pull Requests (Step 4.1) allows you to explicitly manage the "readiness" status of your contribution before it's formally reviewed by the original project's maintainers.

#### Best Practices to Prevent Conflicts
- **Sync First, Always:** Never start new work or a long coding session without first completing Step 1.1 to ensure your fork's `main` branch is current with the original project.
- **Use Short-Lived Branches:** The longer a branch exists without merging, the more it diverges from the original project's `main`, increasing the chance of major and complex conflicts. Aim for small, focused contributions that can be developed and proposed via PR relatively quickly.
- **Sync Your Feature Branch Periodically:** Don't let your feature branch get too far out of sync from the original project's `main` branch. To update it, follow the same process outlined in Step 5 (`git fetch upstream`, `git merge upstream/main`). Good times to sync are:
    - **On a schedule:** For branches that live longer than a day, sync with `upstream/main` every day or two to integrate changes in small chunks.
    - **After a major change:** If you know the original project's maintainers have merged a large feature that might affect your work, sync your active feature branches as soon as possible.
    - **Before creating a Pull Request:** As a final check, sync your branch one last time right before you create your PR (or mark your Draft PR as ready). This lets you handle any last-minute conflicts privately, ensuring a clean pull request for the maintainers.
- **Communicate:** If you are working on a large feature that might conflict with others' work, communicate with the original project's maintainers or other contributors if possible.
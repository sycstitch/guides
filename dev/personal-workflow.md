# My Personal Project Workflow (Git + Docker + CI/CD)

This guide outlines the complete process from starting a new task to seeing it live on the production website for your personal projects. As the owner of the repository, you'll manage development directly through branches and Pull Requests.

---

### 0. One-Time Setup: Initializing Your Local Environment

This section covers how to get your local development environment set up by cloning your repository from GitHub.

1.  **Clone your repository to your machine:**
    If you don't already have a local copy of your repository, use the `git clone` command.
    ```bash
    git clone [https://github.com/](https://github.com/)<your-username>/<repo-name>.git
    cd <repo-name>
    ```
    This command creates a new folder named `<repo-name>` on your machine, which is a local copy of your personal GitHub repository.

2.  **Verify your remote:**
    Once you are inside your project directory, you can verify that Git has correctly set up a remote pointing to your GitHub repository.
    ```bash
    git remote -v
    ```
    You should see `origin` listed with both fetch and push URLs pointing to your GitHub repository (e.g., `https://github.com/<your-username>/<repo-name>.git`). This `origin` is your primary remote for pushing and pulling your work.

---

### 1. Starting a New Feature or Fix

Before writing any new code, it's crucial to prepare your local environment and create a dedicated branch for your task.

* **Step 1.1: Sync Your Local `main` Branch with Remote**
    Always start with the latest version of your project from GitHub. This is the most important step to prevent future merge conflicts by ensuring your local `main` branch is up-to-date.
    ```bash
    # Fetch latest changes from your remote repository (origin)
    git fetch origin

    # Switch to your main branch
    git checkout main

    # Merge the remote main branch's changes into your local main branch
    # This ensures your local main is identical to the latest on GitHub
    git merge origin/main

    # Push the updated local main branch to GitHub
    # (This ensures your remote main is up-to-date, especially if you had local-only commits or just synced)
    git push origin main
    ```

* **Step 1.2: Create a New Branch**
    Create a descriptive branch for your task. It's a best practice to never work directly on your `main` branch. Creating a new branch isolates your work, making it easier to manage and revert if needed.
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
    # In the terminal running Docker Compose (where you ran docker-compose up --build):
    Ctrl+C
    docker-compose down
    ```
    This command stops and removes the containers, networks, and volumes created by `docker-compose up`.

---

### 3. Saving and Sharing Your Progress (Git)

Once you've completed a logical chunk of work (even if the feature isn't finished), save it to your Git repository and push it to GitHub for backup.

* **Step 3.1: Stage and Commit Your Work**
    First, stage your changes to include them in the next commit, then create a commit with a descriptive message. Commit messages should briefly summarize *what* changed and *why*.
    ```bash
    git add .
    git commit -m "feat: Add user profile page foundation"
    ```
    * **Tip:** Commit frequently! Small, focused commits are easier to track, revert, and understand.

* **Step 3.2: Push Your Branch to GitHub**
    After committing locally, push your branch to your remote repository on GitHub. This acts as your offsite backup, protecting your work from local machine failures, and also makes your progress visible (though isolated on its branch) on GitHub.
    ```bash
    git push origin <your-branch-name>
    ```
    * The first time you push a new branch, Git might suggest using `--set-upstream origin <your-branch-name>` to link your local branch to the remote one, simplifying future pushes.

---

### 4. Code Review and Automated Preview (GitHub)

Even for personal projects, leveraging GitHub's Pull Request features is highly beneficial for quality assurance, automated checks, and previewing your work.

* **Step 4.1: Open a Draft Pull Request (Recommended)**
    Go to your repository on GitHub. You'll likely see a prominent banner or prompt indicating that your recently pushed branch has new commits and suggesting you "Compare & pull request."
    * Click this prompt.
    * Fill out a clear title and a detailed description for your proposed changes.
    * **Crucially, mark it as a "Draft Pull Request"** (usually a dropdown option next to the "Create pull request" button).
    * **Why a Draft PR?** A Draft PR clearly signals that your work is still in progress, not ready for review or merging. It allows any configured CI/CD checks (like tests, linters, deployment previews) to run, giving you instant feedback without implying the code is complete. This is how you keep your ongoing work "mostly private" from accidental public consumption.

* **Step 4.2: Wait for Checks and Review the Preview**
    Once your Draft PR is open, any automated CI/CD workflows you have configured will run against your branch.
    * Monitor the checks for success or failure.
    * If your CI/CD includes a deployment preview, the `firebase-hosting-bot` (or similar service) will post a preview URL in the PR. Use this to review your changes in a live environment.
    * If you need to make more changes based on CI/CD feedback or further testing, repeat the local development loop (Step 2), commit, and push (Step 3). Your Draft PR will automatically update with these new commits.
    * **Visibility Note:** While your feature branch is public (as it's on your public repo), the Draft PR explicitly communicates its "work-in-progress" status, preventing premature review or merging.

---

### 5. Handling Merge Conflicts (When They Happen)

Merge conflicts occur when changes in your feature branch overlap with changes that have been made and merged into `main` since you last updated your branch. This process also applies proactively to keep a long-running branch up to date.

* **Step 5.1: Make a Safety Commit**
    Before attempting to merge, commit any work-in-progress on your feature branch to ensure nothing is lost if the merge process goes awry.
    ```bash
    git add .
    git commit -m "wip: saving before merge"
    ```

* **Step 5.2: Update Your Local Repo with the Latest `main`**
    Make sure you are on your feature branch locally (`git checkout <your-branch-name>`), then fetch the latest changes from your `origin` remote.
    ```bash
    # Fetch the very latest changes from your remote repository
    git fetch origin
    ```

* **Step 5.3: Merge the Latest `main` into Your Feature Branch**
    This command will attempt to integrate the latest code from `origin/main` into your current feature branch. If there are conflicting changes, Git will pause and notify you.
    ```bash
    git merge origin/main
    ```
    Your terminal will now likely show an error and list the conflicted files.

* **Step 5.4: Resolve Conflicts Locally**
    - Open your project in your code editor. Git will mark conflicting files with special markers (`<<<<<<<`, `=======`, `>>>>>>>`).
    - Manually edit each conflicted file to remove these markers and decide what the final, correct version of the code should be. You'll choose to keep your changes, the incoming changes from `main`, or a combination of both.

    <details>
    <summary><i>Click here for a detailed guide using VS Code's built-in conflict resolution</i></summary>

    - Your conflicting files will be highlighted in the Source Control panel.
    - When you open a conflicted file, VS Code provides inline helper buttons over each conflict block:
        - **`Accept Current Change`**: Keeps only your changes from your branch.
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
    After successfully merging `main` into your feature branch and committing the merge, push these changes to your remote branch on GitHub.
    ```bash
    git push origin <your-branch-name>
    ```
    The Pull Request on GitHub will automatically update, and the conflict warning (if any) should disappear.

---

### 6. Merging and Deploying to Production

Once your feature is complete, thoroughly tested, and ready for your users, you'll merge it into your `main` branch and deploy.

* **Step 6.1: Merge Your Pull Request**
    After your work is complete, all automated checks pass, and you are satisfied with the changes, you will merge your Pull Request into your `main` branch. As the repository owner, you'll perform this step directly on GitHub.
    * On your Pull Request page, if it's a Draft, first click "Ready for review."
    * Then, click the "Merge pull request" button (or "Squash and merge" if you prefer a cleaner `main` history with fewer commits).
    * Confirm the merge.

* **Step 6.2: Watch the Final Deployment**
    After merging, your CI/CD pipeline (configured to trigger on merges to `main`) will automatically deploy your changes to the production website. Monitor the CI/CD status to ensure a successful deployment.

* **Step 6.3: Clean Up**
    Once your feature is live and stable on production, you can clean up your repository by deleting the now-merged feature branch.
    * On GitHub, after merging a PR, you'll typically see a "Delete branch" button. Click it.
    * Optionally, delete the local branch as well:
        ```bash
        git branch -d <your-branch-name>
        ```

---

### Notes

#### General Best Practices
- **Sync Your `main` Regularly:** Even when you aren't actively working on a feature, it's good practice to periodically repeat Step 1.1 to keep your local `main` branch from getting stale. This reduces the chances of large merge conflicts later.
- **You Can Have as Many Feature Branches as You Want:** Don't hesitate to create new branches for every distinct task or idea.
- **Work Isolation & Backup:** Your work is isolated on its own branch. By consistently pushing your commits to GitHub (Step 3.2), you ensure your work is backed up offsite, protecting it against local machine failures, while using Draft PRs (Step 4.1) allows you to manage its "readiness" status.

#### Best Practices to Prevent Conflicts
- **Sync First, Always:** Never start new work or a long coding session without first pulling the latest changes into your `main` branch (Step 1.1) and ensuring your feature branch is up-to-date.
- **Use Short-Lived Branches:** The longer a branch exists without merging, the more it diverges from `main`, increasing the chance of major and complex conflicts. Aim for small, focused features that can be developed and merged relatively quickly.
- **Sync Your Feature Branch Periodically:** Don't let your feature branch get too far out of sync from the `main` branch. To update it, follow the same process outlined in Step 5 (`git fetch origin`, `git merge origin/main`). Good times to sync are:
    - **On a schedule:** For branches that live longer than a day, sync with `main` every day or two to integrate changes in small chunks.
    - **After a major change:** If you know you've merged a large feature into `main` that might affect your work, sync your active feature branches as soon as possible.
    - **Before creating a Pull Request:** As a final check, sync your branch one last time right before you convert your Draft PR to "Ready for review" or merge it. This lets you handle any last-minute conflicts privately, ensuring a clean merge.

# My Daily Development Workflow (Git + Docker + CI/CD)

This guide outlines the complete process from starting a new task to seeing it live on the production website.

### 1. Starting a New Feature or Fix

Before writing any code, prepare your local environment and branch.

* **Step 1.1: Sync Your `main` Branch**
    Always start with the latest version of the project.
    ```bash
    # Switch to your main branch
    git checkout main

    # Pull the latest changes from GitHub
    git pull origin main
    ```

* **Step 1.2: Create a New Branch**
    Create a descriptive branch for your task. Never work directly on `main`.
    ```bash
    # Create and switch to a new branch
    # Good examples: feat/user-profile, fix/login-button
    git checkout -b <type/your-branch-name>
    ```

### 2. The Local Development Loop (Code & Test)

This is where you'll spend most of your time. You will use Docker to run a live, local version of your entire Firebase environment.

* **Step 2.1: Start the Local Environment**
    This command builds your Docker image (if needed) and starts all your emulators.
    ```bash
    # Run this once at the beginning of your coding session
    # The --build flag is only necessary if you changed package.json or the Dockerfile
    docker-compose up --build
    ```
    Wait for the message: `✔ All emulators ready!`

* **Step 2.2: Code and See Instant Changes**
    This is your fast "inner loop", made possible by Docker Volumes.
    - Open your project in VS Code and edit the files in your `./public` directory.
    - Save a file.
    - Refresh your browser at **http://localhost:5000** to see your changes instantly.
    - You can also visit **http://localhost:4000** to see the Emulator UI dashboard.
    - Repeat this process as you build out your feature. No rebuilding the container is needed for code changes in synced volumes.

* **Step 2.3: Stop the Environment**
    When you're finished coding for the session or are ready to commit your work:
    1.  Go to the terminal where Docker Compose is running and press `Ctrl+C`.
    2.  Run the following command to cleanly stop and remove the containers and networks, freeing up your computer's resources.
        ```bash
        docker-compose down
        ```

### 3. Saving and Sharing Your Progress (Git)

Once you've completed a logical chunk of work, save it to your Git repository. You can do this many times for a single feature.

* **Step 3.1: Stage and Commit Your Work**
    ```bash
    # Stage all your changed files for the commit
    git add .

    # Commit the changes with a descriptive message
    git commit -m "feat: Add user profile page foundation"
    ```

* **Step 3.2: Push Your Branch to GitHub**
    This uploads your commits to the remote repository.
    ```bash
    git push origin <your-branch-name>
    ```

### 4. Code Review and Automated Preview (GitHub)

Now you move from your local machine to the GitHub website.

* **Step 4.1: Open a Pull Request**
    - Go to your repository on GitHub.
    - Click the "Compare & pull request" button for the branch you just pushed.
    - Give it a clear title and a detailed description.

* **Step 4.2: Wait for Checks and Review the Preview**
    - Your CI/CD pipeline will automatically run.
    - Wait for all checks to pass.
    - The `firebase-hosting-bot` will post a comment with a unique **preview URL**. Click this link to see and test your changes in a live environment.
    - If you find a bug or need to make more changes, simply go back to **Phase 2**, make your edits, and `commit` and `push` to this same branch. The Pull Request will update automatically.

### 5. Merging and Deploying to Production

This is the final step after your PR has been approved and all checks are green.

* **Step 5.1: Squash and Merge**
    - On the GitHub Pull Request page, select **"Squash and merge"** from the merge dropdown menu. This keeps your `main` branch history clean.
    - Confirm the merge.

* **Step 5.2: Watch the Final Deployment**
    - The merge will automatically trigger your final GitHub Action. You can watch its progress on the "Actions" tab.
    - When it finishes, your changes are now live on your production URL!

* **Step 5.3: Clean Up**
    - GitHub will show a button to **"Delete branch"**. It is safe and good practice to click this, as the branch's work is now part of `main`.

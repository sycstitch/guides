### **Brief Guide: Creating Clear Git Branch Names**

Good branch names are essential for understanding your project's history and navigating your work. Aim for clarity and consistency.

**Core Components:**

1.  **Type Prefix:** Start with a short prefix indicating the *purpose* of the branch.
2.  **Concise Description:** Follow with a brief, meaningful summary of the work.

**1. Choose a Type Prefix (Common Examples):**

| Prefix      | Purpose                                            | Example                          |
| :---------- | :------------------------------------------------- | :------------------------------- |
| `feat/`     | New feature development                            | `feat/user-onboarding`           |
| `fix/`      | Bug fixes                                          | `fix/login-bug`                  |
| `chore/`    | Routine tasks, build process, dependency updates   | `chore/update-deps`              |
| `docs/`     | Documentation changes                              | `docs/api-guide`                 |
| `refactor/` | Code restructuring without changing behavior       | `refactor/auth-service`          |
| `hotfix/`   | Urgent bug fix for production (usually from `main`)| `hotfix/critical-vulnerability`  |

**2. Add a Concise Description:**

* **Be Specific:** Summarize *what* the branch aims to achieve.
* **Use Kebab-Case:** All lowercase, words separated by hyphens (e.g., `my-new-feature`, not `myNewFeature` or `my_new_feature`).
* **Avoid Spaces, Special Characters (except hyphens), and Emojis.**
* **Keep it Relatively Short:** Long names are cumbersome.

**Example Branch Names:**

* `feat/dark-mode-toggle`
* `fix/broken-password-reset`
* `chore/upgrade-node-v18`
* `docs/add-contributing-guide`
* `refactor/extract-helper-functions`
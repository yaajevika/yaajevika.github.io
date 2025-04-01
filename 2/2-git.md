# Version Control With Git
## Introduction

In this article, we’ll explore:

1. **What Git Is**
2. **Popular Git Hosting Services**
3. **Other Version Control Systems**
4. **Git Basics: Repositories, Branches, Commits, Pull Requests, & Conflicts**
5. **Practical Steps: Setting Up Our Unsplash App Repository**

---

## 1. What Is Git?

**Git** is a **version control system (VCS)**—a tool that keeps track of **every change** made to a set of files (usually code). Here’s an easy analogy:

- Think of your project as a **scrapbook** of images and text. If you didn’t have Git, you might save a new copy of the scrapbook every time you made changes, like “scrapbook_v2,” “scrapbook_v3,” etc. This quickly becomes confusing.
- With Git, you store just **one** version of your scrapbook but can always see each change made over time. It’s like having an unlimited **undo/redo** history, plus a system that can handle multiple people editing at once.

### Key Benefits of Git:

- **Collaboration:** Multiple people can work on the same project simultaneously without overriding each other’s work.
- **History:** You can travel back in time to any previous version of your project.
- **Backup:** Your changes exist on your local machine **and** can be pushed to a remote server, ensuring you don’t lose your progress.
- **Branching:** You can create separate “copies” of your project to try out new ideas or features, then merge them back later.

---

## 2. Popular Git Hosting Services

### GitHub

- **GitHub** is currently the world’s most popular platform for hosting Git repositories. It’s free for public and private projects (with certain limitations).
- It offers a friendly user interface for code reviews, issue tracking, and continuous integration (CI).

### GitLab

- **GitLab** provides similar functionality to GitHub, often used by enterprises because it can be self-hosted.
- It includes integrated CI/CD (Continuous Integration/Continuous Deployment) pipelines and robust project management features.

### Bitbucket

- **Bitbucket** is another Git-based service from Atlassian (the same company behind Jira).
- Often popular with businesses already using other Atlassian products.

*(All three platforms do essentially the **same** thing: they host your Git repository and provide tools for collaboration. The choice often depends on personal preference or organizational requirements.)*

---

## 3. Other Version Control Systems

Before Git became the dominant standard, other systems were widely used:

- **Subversion (SVN):** A centralized version control system—everyone works from one central repository. You can’t commit changes if you lose connection to that server.
- **Mercurial (hg):** Distributed, similar to Git, but less common nowadays in iOS projects.
- **Perforce:** Used in gaming and large enterprises, can handle massive files but less flexible for distributed teams.

Even though these older systems exist, **Git** stands out today because it’s **fast, flexible, and widely supported** by modern development tools.

---

## 4. Detailed Git Concepts

### 4.1. Repositories

A **Git repository (repo)** is basically a folder (on your computer) that Git tracks. It contains:

- **Your project files** (like Swift code, Xcode files, images).
- A hidden `.git` folder that stores all version history and configuration.

You can have a **local repository** on your machine and a **remote repository** on GitHub or GitLab. You **sync** changes between them by using commands like **push** (send commits to the server) and **pull** (fetch and integrate commits from the server).

### Example Visual (Local vs. Remote)

```
 Local Repository        Remote Repository
       +---+                   +-------+
       | .git (history)       | .git   |
       | project files        | master |
       +---+                   +-------+
         |      push/pull        ^
         |------------------------|

```

### 4.2. Working Directory, Staging Area, and Commits

- **Working Directory:** The actual files/folders you’re editing in Xcode or any editor.
- **Staging Area:** Think of this like a shopping cart. You pick which changes you want to include in your next **commit**. You add changes with `git add <filename>` or `git add .`.
- **Commit:** A snapshot of your changes recorded with a message, e.g., “Add login screen.” Commits form the timeline of your project’s evolution.

### Example Workflow

1. Make changes to `LoginViewController.swift`.
2. Stage them:

    ```bash
    git add LoginViewController.swift

    ```

3. Commit them with a message:

    ```bash
    git commit -m "Implement login functionality"

    ```

4. Push them to the remote repository (assuming your branch is `main`):

    ```bash
    git push origin main

    ```


### ASCII Diagram (Working Directory → Staging Area → Repository)

```
+------------------+
| Working Directory|
|  (edited files)  |
+---------+--------+
          | git add
          v
+------------------+
|  Staging Area    |
|  (files ready    |
|   to commit)     |
+---------+--------+
          | git commit
          v
+------------------+
|   Local Repo     |
| (committed files)|
+------------------+
          | git push
          v
+------------------+
| Remote Repo (GitHub)
|  (shared repository) |
+------------------+

```

### 4.3. Branching

**Branches** in Git let you create an isolated environment for new features or fixes:

- **Main (or Master) Branch:** Typically holds the stable version of your project.
- **Feature Branches:** For example, if you’re working on a login feature, you might create a branch named `feature/login-screen`.

By branching, you don’t disturb the main codebase. Once your feature is tested, you can **merge** it into `main`. If the feature doesn’t work out, you can discard the branch without affecting other parts of the project.

### ASCII Diagram (Basic Branching)

```
         (main)
           |
           o--- Commit 1
           |
           o--- Commit 2
           |\\
           | \\
           |  o--- Commit 3 (feature branch)
           |  |
           |  o--- Commit 4
           | /
           |/
           o--- Merge feature back into main
           |
           o--- Continue...

```

### 4.4. Pull Requests (PRs) / Merge Requests

A **Pull Request (PR)** is a request to merge your branch’s changes into another branch (often `main`). It’s your way of saying, “Hey team, I’ve finished this work. Please review and merge it.”

**Why PRs Are Crucial:**

- They allow **code reviews**: teammates can comment on your changes, suggest improvements, or catch bugs.
- They maintain a clean **commit history**: you can see exactly what each feature branch did before merging.
- They foster better **collaboration**: everyone sees what’s being merged and why.

### ASCII Diagram (Pull Request Flow)

```
 Developer     GitHub (Remote)         Teammates
     |               |                      |
     |--- push --->  |                      |
     |               |--- PR creation ----->|
     |               |                      |
     |               |<---- Review, comment |
     |               |                      |
     |<--- feedback- |                      |
     |--- fix&push-> |                      |
     |               |--- Merge PR          |

```

### 4.5. Handling Merge Conflicts

A **merge conflict** happens when two people edit the same part of the same file. Git can’t automatically decide whose change to keep. For instance:

- Alice changes line 25 of `LoginViewController.swift`.
- Bob also changes line 25 of `LoginViewController.swift`.
- When Bob tries to merge his changes after Alice’s, Git will stop and say there’s a conflict on line 25.

### Conflict Resolution Steps:

1. Open the file in conflict; you’ll see markers like `<<<<<<< HEAD` and `>>>>>>> feature/login-screen`.
2. Decide which version of the code to keep (or combine parts of each).
3. Delete the conflict markers and save the file.
4. **Stage** the resolved file:

    ```bash
    git add LoginViewController.swift

    ```

5. **Commit** the resolution with a message like:

    ```bash
    git commit -m "Resolve merge conflict in LoginViewController"

    ```

6. Proceed with the merge.

### 4.6. The .gitignore File

A **.gitignore** file is a simple text file in your repository that tells Git which files or folders to **ignore**. This prevents unneeded or sensitive files (like secrets, credentials, build artifacts, or large cached data) from being tracked and pushed into the remote repository.

For iOS projects, a typical `.gitignore` might include:

```
# Xcode
build/
DerivedData/
*.xcuserdata/

# macOS
.DS_Store

# Swift Package Manager
/.build/
*.xcodeproj/

# Cocoapods
Pods/

# Carthage
Carthage/

# SwiftPM Packages
*.swiftpm/

# Other
*.log

```

**How to Use It:**

1. Create a file named `.gitignore` in the **root** of your project.
2. Paste in the patterns corresponding to files and folders you do **not** want to commit.
3. Save and commit `.gitignore` early (ideally right after creating a repository) so extraneous files aren’t tracked at all.
4. If you add entries later, you might need to manually untrack files that are already in the repo using `git rm --cached <filename>` if they were committed before.

Including a `.gitignore` file is considered **best practice** in almost every Git-based project, especially to keep your repository clean from build artifacts and large or sensitive files that don’t belong in version control.

### 4.7. Branching Strategies in Projects

Beyond creating simple feature branches, many development teams follow **branching strategies** to handle releases, bug fixes, and ongoing development more systematically. Here are a few common approaches:

### 4.7.1. Git Flow

- **Main Branch (or Master):** Always reflects a production-ready state.
- **Develop Branch:** An integration branch for features before they’re released.
- **Feature Branches:** Branch off from develop, e.g., `feature/new-login`.
- **Bugfix Branches:** Branch off from develop for bug fixes, e.g., `bugfix/login-bug`.
- **Release Branches:** Created from develop when preparing a new release, e.g., `release/1.0.0`.
- **Hotfix Branches:** Branch off from main for urgent fixes, e.g., `hotfix/critical-bug`.

Workflow in Git Flow:

1. **New Feature?** Create a feature branch from `develop`.
2. **Feature Done?** Merge feature branch into `develop`.
3. **Ready to Release?** Create a release branch from `develop`.
4. **Release Branch Stabilized?** Merge it back into both `develop` and `main` (the `main` now represents the new production release).
5. **Found a Critical Bug in Production?** Create a hotfix branch from `main`, fix the bug, then merge it back to both `main` and `develop`.

This strategy is highly structured, good for large teams or projects with scheduled releases.

### 4.7.2. GitHub Flow

- **Main Branch:** Contains deployable code at all times.
- **Feature Branches:** Branch off from `main` for new features or fixes, merge back via Pull Requests.
- **Continuous Deployment/Integration:** Merges to main can be automatically deployed if tests pass.

Workflow in GitHub Flow:

1. **Create a Branch** for a feature or bug fix from `main`.
2. **Work & Commit** changes in that branch.
3. **Open a Pull Request** against `main`.
4. **Review & Merge**: Once approved, merge the branch back into `main`.
5. **Deploy Immediately** if your CI/CD pipeline is set up to do so.

This strategy is simpler and works well for teams practicing continuous delivery.

### 4.7.3. Trunk-Based Development

- **Trunk (Main)**: All developers commit frequently to a single shared branch (the trunk).
- **Short-Lived Branches**: If used at all, they’re merged back quickly (in hours or a day).
- **Feature Flags**: Large or risky changes are hidden behind feature flags in code so they can be toggled off in production if unstable.

Trunk-Based Development focuses on rapid integration and continuous merges, minimizing long-lived branches.

### 4.7.4. Handling Bug Fixes & Releases

- **Bug Fixes**: In many strategies, bug fixes are done in either short-lived feature branches (if discovered in development) or hotfix branches (if discovered in production). The key is to isolate the fix, test it, and merge it quickly.
- **Release Branches**: Some teams create specific branches to finalize a major release. They’ll test, fix final bugs, and tag a version (e.g., `v1.0.0`). Once stable, the release branch merges back to main (and possibly to develop if you’re using Git Flow).
- **Tags**: When a release is finalized, many teams create a Git **tag** (e.g., `v1.2.3`) on the commit that represents that release. This makes it easy to identify and roll back to a known stable version if needed.

**In Real Projects:**

- A continuous integration (CI) pipeline typically runs whenever a feature branch or main branch gets new commits.
- Code reviews happen via Pull Requests before merging changes.
- Larger teams might adopt Git Flow or trunk-based dev to coordinate complex releases. Smaller teams might stick to simple GitHub Flow for agility.
- Hotfixes are usually branched off and merged back quickly to keep production stable.

---

## 5. Homework (After Git Presentation)

1. **Clone the Repo & Set Up Remote Access**
    - Each intern must clone or fork the `UnsplashApp` repo to their local machine.
    - Configure Git to work with HTTPS or SSH keys.
2. **Pick a Preferred Git App or Terminal**
    - If you’re not comfortable with command-line Git, try using **GitHub Desktop**, **Sourcetree**, or Xcode’s built-in Git support.
3. **Commit and Make a Simple PR**
    - Create a new branch (e.g., `feature/add-contributor-name`).
    - Edit a file (like `README.md`) to add your name under “Contributors.”
    - Stage and commit the change:

        ```bash
        git add README.md
        git commit -m "Add John Doe to Contributors"

        ```

    - Push your branch:

        ```bash
        git push origin feature/add-contributor-name

        ```

    - Open a PR on GitHub to merge `feature/add-contributor-name` into `main`.
    - Request a review from your team member.

---

## Conclusion

With Git fundamentals—branching, commits, pull requests, conflict resolution —under your belt, you can collaborate confidently and keep your code organized.

 Next, we’ll tackle **Application Architecture**, where we’ll explore clean code practices, SOLID principles, and how to use Swift Package Manager for modular iOS development. Get ready to structure your app the right way!

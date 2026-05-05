---
icon: simple/github
#TODO: Need to talk about git orgs
---

# Git & GitHub Setup

To collaborate with your team, you need to configure Git on your computer and connect it to GitHub. This is a one-time setup process for your laptop.

## 1. Create a GitHub Account
If you don't have one, go to [GitHub.com](https://github.com/) and create an account. 

!!! tip "GitHub Student Developer Pack"
    Use your school email to sign up for the [Student Developer Pack](https://education.github.com/pack). It gives you free access to GitHub Pro, Copilot, and premium developer tools while you are a student!

## 2. Install Git
Even if you use visual tools later, you must install the core Git engine so your code editors (like WPILib VS Code) can track changes.

=== "Windows"
    Download and install [Git for Windows](https://gitforwindows.org/). When clicking through the installer, you can safely leave all the default options checked.

=== "macOS"
    Open your terminal and type `git --version`. If it is not installed, your Mac will automatically prompt you to install the Xcode Command Line Tools (which includes Git).

=== "Linux"
    Run the install command for your package manager:
    `sudo apt install git` (Ubuntu/Debian) or `sudo pacman -S git` (Arch).

---

## 3. Authenticate & Download Your Code

Choose the method that works best for you. We **highly recommend GitHub Desktop** for new programmers on Windows and Mac, as it prevents common terminal mistakes.

=== "GitHub Desktop (Recommended for Windows/Mac)"

    GitHub Desktop is an official visual app that makes logging in and downloading code incredibly easy. You won't even need to use the terminal!

    **Step A: Install & Log In**

    1. Go to [desktop.github.com](https://desktop.github.com/) and download the app.
    2. Open GitHub Desktop. It will immediately ask you to **Sign in to GitHub.com**. 
    3. Click the button, which will open your web browser to authorize the app.
    4. The app will prompt you to configure Git by verifying your Name and Email. Leave them as the defaults it grabbed from your account and click **Finish**.

    **Step B: Clone (Download) the Code**

    1. In GitHub Desktop, click **File > Clone Repository** (or click the green "Clone a repository from the Internet" button).
    2. Click the **GitHub.com** tab. You will see a list of every repository your team has given you access to.
    3. Select your robot code (e.g., `RobotCode2026`).
    4. Choose a "Local Path" (where the folder will live on your computer, like your Documents folder).
    5. Click **Clone**. You can now open that folder in WPILib VS Code!

=== "Command Line (For Linux or Terminal Users)"

    If you are on Linux or prefer to live in the terminal, we use the **GitHub CLI** tool to authenticate securely via your web browser.

    **Step A: Configure Your Identity**
    Tell Git who you are so your team knows who wrote what code. Open your terminal and run:
    ```bash
    git config --global user.name "Firstname Lastname"
    git config --global user.email "your.email@example.com"
    ```

    **Step B: Install & Log in with GitHub CLI**
    
    1. Go to [cli.github.com](https://cli.github.com/) and follow the install instructions for your OS.
    2. Open your terminal and run:
    ```bash
    gh auth login
    ```
    3. Use your arrow keys to select: **GitHub.com** -> **HTTPS** -> **Yes** -> **Login with a web browser**.
    4. Copy the one-time code generated in the terminal, press ++enter++ to open your browser, paste the code, and click Authorize.

    **Step C: Clone the Code**
    Go to your team's GitHub repository, click the green **Code** button, select the **HTTPS** tab, and copy the web link. In your terminal, navigate to where you want the folder to live and type:
    ```bash
    git clone https://github.com/YourTeam/RobotCode2026.git
    ```
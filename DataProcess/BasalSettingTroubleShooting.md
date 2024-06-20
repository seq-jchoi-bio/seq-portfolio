© 2022 Janghyun Choi<br>This work is licensed under a [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-nc-sa/4.0/).<br>![Docer](https://img.shields.io/badge/Revised%20version-5%2E15%2E24-green?style=flat&logo=Markdown&colorA=black) [![CC BY-NC-SA 4.0](https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-green?labelColor=black)](http://creativecommons.org/licenses/by-nc-sa/4.0/)

This section offers a comprehensive guide to the fundamental aspects of installing and maintaining software via package managers, specifically focusing on Python, Java, and R. Additionally, it outlines potential solutions to common challenges encountered on silicon Mac computers. This section is organized as follows:

**1. [Converting Shell Environment and Installing Homebrew](#shell-environment)**

**2. [Setting Up Anaconda in Linux](#anaconda-setup)**

**3. [Installing Java JDK 16 in Linux](#installing-java)**

**4. [Troubleshooting in R for macOS](#troubleshooting-r)**

**Disclaimer**: The provided solutions are based on individual experiences and are not comprehensive. They should be considered as general guidance only.

<a name="shell-environment"></a>
# Converting Shell Environment and Installing Homebrew

Homebrew is an open-source package manager for macOS and Linux systems. It simplifies the process of installing and managing software, enabling users to easily install various packages with a single command line instruction. Using the `brew` command, Homebrew allows for the straightforward installation, update, and removal of packages. It also provides an environment where users can create or modify packages, fostering a community-driven platform for extensions. Additionally, Homebrew automates dependency management, ensuring that various software can coexist without conflicts. This feature makes it an essential tool for many developers and researchers.

#### Step 1: Convert to Z shell in macOS
Zsh (Z Shell) is favored among developers and power users for its advanced features and user-friendly experience. It offers robust autocomplete capabilities, which streamline command input and enhance productivity. Zsh is also known for its powerful file globbing, allowing intricate file manipulation and pattern matching. Users appreciate its modular configuration, enabling detailed customization of prompts, themes, and shell options through frameworks like Oh My Zsh. Additionally, Zsh supports complex scripting with enhanced built-in functions and improved mathematical and array operations, making it a popular choice over other shells. **Note**: Starting with macOS 10.15, the default shell is zsh.

- Check your shell environment and convert to zsh:
    ```bash
    # Check your current shell
    $ echo $SHELL

    # Check the list of acceptable shells and convert if you are currently using bash.
    $ cat /etc/shells 

    # Convert to zsh
    $ chsh -s /bin/zsh 
    ```
#### Step 2: Install Homebrew and Its Recommended Plugins
- Type the following command to install homebrew:
```bash
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
- Installing **oh-my-zsh**:
    1. Install the plugin:
        ```bash
        $ sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
        ```
    2. After installation, you will see a screen where you can customize your shell environment.
    3. Open the shell configuration file with text editor
        ```bash
        $ nano ~/.zshrc
        ```
    4. Find the syntax `ZSH_THEME=”rubbyrussell“` followed by the name of the theme you want between the double quotes. You can check out the available themes on the [oh-my-zsh GitHub](https://github.com/ohmyzsh/ohmyzsh/wiki/Themes).
        ```bash
        ZSH_THEME="clean"
        ```
    - **Note**: In programming languages like shells (ZSH/Bash), Python, and R, the `#` symbol marks comments that are not executed.
    
    5. Save and reload the shell
        ```bash
        $ source ~/.zshrc
        ```

- Installing **Syntax highlighting**:
    1. install the plugin:
        ```bash
        $ brew install zsh-syntax-highlighting
        ```
    2. Open the shell configuration file.
    3. Add the following syntax in the file:
        ```bash
        source /path/to/your/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
        # For example
        source /usr/local/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
        ```
    4. Save and reload the shell.

#### In the Case of Linux (Ubuntu 20.04.2 LTS)
1. Check your shell environment using `echo $SHELL`.
2. Install and convert to zsh as the following commands:
    ```bash
    $ sudo apt install zsh
    $ chsh -s /usr/bin/zsh
    ```

3. Install essential libraries, containing C/C++ compiler, GNU make, curl, and git, as the follwing command:
    ```bash
    $ sudo apt-get install build-essential curl file git
    ```
4. Install homebrew:
    ```bash
    $ sh -c "$(curl -fsSL https://raw.githubusercontent.com/Linuxbrew/install/master/install.sh)"
    ```

5. Open the configuration file and set up the necessary environment paths:
    ```bash
    # Open the shell configuration file
    $ nano ~/.zshrc 
    # Add path settings
    export PATH="/path/to/your/linuxbrew/.linuxbrew/bin:$PATH"
    export MANPATH="/path/to/your/linuxbrew/.linuxbrew/share/man:$MANPATH"
    export INFOPATH="/path/to/your/linuxbrew/.linuxbrew/share/info:$INFOPATH"

    # For example
    export PATH="/home/linuxbrew/.linuxbrew/bin:$PATH"
    export MANPATH="/home/linuxbrew/.linuxbrew/share/man:$MANPATH"
    export INFOPATH="/home/linuxbrew/.linuxbrew/share/info:$INFOPATH"
    ```

6. Save and reload the shell.
**Note**: Plugin for homebrew installation for Linux mirrors the process on macOS.

<a name="anaconda-setup"></a>
# Setting Up Anaconda in Linux

Anaconda is a popular distribution of Python and R programming languages for scientific computing, aimed at simplifying package management and deployment. It includes a wide range of pre-installed packages geared towards data science and machine learning. On macOS, Anaconda can be installed via a `.pkg` file, allowing for a straightforward setup without the need for additional configurations. However, on Linux, after installing Anaconda, additional configuration steps are typically required to fully integrate and utilize all features of the Anaconda environment.

#### Step-by-Step Guide
1. **Checking installed Python version**
- Ensure that Python is installed, as Ubuntu 20.04 LTS comes with Python pre-installed, and update your system’s package list and upgrade Python:
    ```bash
    $ python3 --version

    $ sudo apt-get update
    $ sudo apt-get upgrade python3
    ```

2. **Installing essential libraries and Pypi3 management package:**
    ```bash
    $ sudo apt-get install build-essential checkinstall
    $ sudo apt-get install libreadline-gplv2-dev libncursesw5-dev libssl-dev \
        libsqlite3-dev tk-dev libgdbm-dev libc6-dev libbz2-dev libffi-dev zlib1g-dev
    $ sudo apt-get install python3-pip
    ```

3. **Installing and updating Anaconda:**
- Download the Anaconda installer script from the Anaconda website and execute it.
    ```bash
    $ cd Downloads
    $ sudo bash Ana*.sh
    ```
- Follow the on-screen instructions to agree to the license, choose the install location, and decline the initialization.
- Ensure the base version of conda is up-to-date:
    ```bash
    $ conda update -n base conda
    ```

4. **setting path for Anaconda and updating essential Anaconda components**
- Open the shell file and add your Anaconda PATH:
    ```bash
    export PATH="/path/to/your/anaconda/anaconda3/bin:$PATH"

    # For example
    export PATH="/home/jh/anaconda3/bin:$PATH"
    ```
- Reload the shell configuration to apply changes, and make sure all components are up-to-date:
    ```bash
    $ sudo apt-get update
    $ conda update xz
    ```
#### Troubleshooting Library Issues
- Resolve issues related to shared library dependencies as bellow:
    ```
    XXXXXXXX: error while loading shared libraries: libssl.so.1.0.0: cannot open shared object file: No such file or directory
    ```
- **Solution**: 
    ```bash
    # If encountering shared library errors, execute the following:
    $ sudo nano /etc/apt/sources.list

    # Add the line in the source file and save it:
    deb http://security.ubuntu.com/ubuntu bionic-security main

    $ sudo apt update && apt-cache policy libssl1.0-dev
    $ sudo apt-get install libssl1.0-dev
    ```

<a name="installing-java"></a>
# Installing Java JDK 16 in Linux

Java is a powerful programming language used by millions of developers worldwide, essential for developing servers, mobile apps, and large systems across various platforms. The Java Development Kit (JDK) provides the necessary compiler, libraries, and other tools required for Java application development. This section outlines step-by-step instructions for installing Java JDK 16 on Linux operating systems. 

#### Step-by-Step Installation
1. Download the JDK files suitable for your operating system from the Oracle website. It is recommended to download the gz compressed file (e.g., jdk-16_linux-x64_bin.tar.gz), not rpm or deb formats.

2. Move the downloaded file and extract it:
    ```bash
    $ sudo mv jdk-16_linux-x64_bin.tar.gz /usr/local/java/
    $ cd /usr/local/java/
    $ sudo tar xvfz jdk-16_linux-x64_bin.tar.gz
    ```

3. Set Java environment and update system alternatives:
    ```bash
    $ sudo nano /etc/profile

    # Add the following line at the end of the file, and then save it:
    export JAVA_HOME=$(readlink -f /usr/bin/java | sed "s:bin/java::")

    # Exit then type the following commands
    $ sudo update-alternatives --install "/usr/bin/java" "java" "/usr/local/java/jdk-16/bin/java" 1
    $ sudo update-alternatives --install "/usr/bin/javac" "javac" "/usr/local/java/jdk-16/bin/javac" 1
    $ sudo update-alternatives --set java /usr/local/java/jdk-16/bin/java
    $ sudo update-alternatives --set javac /usr/local/java/jdk-16/bin/javac
    ```

4. Reload profile and verify installation
    ```bash
    $ . /etc/profile
    ``` 
    ```
    > $ java -version
    # Expected output:
    # java version "16" 2021-03-16
    # Java(TM) SE Runtime Environment (build 16+36-2231)
    # Java HotSpot(TM) 64-Bit Server VM (build 16+36-2231, mixed mode, sharing)
    ```
#### Alternative Installation Method (Not Recommended)

1. Add repository:
    ```bash
    $ sudo add-apt-repository ppa:linuxuprising/java
    $ sudo apt update
    ```

2. Change to root user and set repository
    ```bash
    $ su -
    `#` echo "deb http://ppa.launchpad.net/linuxuprising/java/ubuntu focal main" | tee /etc/apt/sources.list.d/linuxuprising-java.list
    `#` apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 73C3DB2A
    `#` apt-get update
    `#` exit  # Logout from root
    ```
3. Install Oracle Java 16 via apt
    ```bash
    $ sudo apt install oracle-java16-installer --install-recommends
    ```

#### Troubleshooting
**Problem 1. Authentication Failure**
- If you have never switched super root before and do not know the password, you will see the following Authentication Error:
    ```
    > su -
    Password: 
    su: Authentication failure
    ```
- You can set the password with the command below:
    ```bash
    $ sudo passwd
    ```
    ```
    XXX ~$ > sudo passwd
    New password: # silent password prompt
    Retype new password: # silent password prompt

    XXX ~$ > su -
    Password: # silent password prompt
    XXX ~# >
    ``` 
**Note**. **1.** When entering a password, a silent password prompt is applied. **2.** When your account is switched to super root, the dollar marks ($) are replaced with sharp marks (#).

**Problem 2. Installing Error**
- During the installation process, it is possible to encounter an error similar to the one described below:
    ```
    Oracle JDK 16 is NOT installed.
    dpkg: error processing package oracle-java16-installer (--configure):
    installed oracle-java16-installer package post-installation script subprocess returned error exit status 1
    Errors were encountered while processing:
    oracle-java16-installer
    E: Sub-process /usr/bin/dpkg returned an error code (1)
    ```
- Solution: 
    ```bash
    $ sudo rm /var/lib/dpkg/info/oracle-java16-installer.postinst -f
    $ sudo dpkg --configure oracle-java16-installer
    ```

<a name="troubleshooting-r"></a>
# Troubleshooting in R for macOS

### Problem 1: Adjusting Language Setting in Shell
Sometimes, RStudio may default to the system language, or you may prefer to use RStudio in a different language. For Mac users, setting RStudio to use English (US) can be accomplished by modifying the system locale.
#### Solution 1: Setting Language Environment in Shell
This guide provides steps to change the default language of the shell environment in macOS, which affects all applications run from the shell.
1. Open the zsh configuration file (`.zshrc`) using text editors.
2. Add the following line to set the default shell language to English (US):
    ```bash
    export LANG=en_US.UTF-8
    ```
3. Source the `.zshrc` to apply the changes immediately.

#### Solution 2: Change Language to English in RStudio
This solution ensures that RStudio runs in English, regardless of your Mac’s system language settings, providing a consistent experience in the specified language.
1. Open your Terminal and type the following command to enforce English as the default language for R:
     ```bash
     defaults write org.R-project.R force.LANG en_US.UTF-8
     ```
2. For the change to take effect, you might need to restart your Terminal or RStudio. This command adjusts the locale specifically for R, making RStudio display menus and messages in English (US).

### Problem 2: Disappearance of Library Packages Post R Update
When R is updated to a new minor version, previously installed library packages might not be recognized due to changes in the library path. These packages are not lost but are just not linked to the new version.

#### Solution: Set the Path to the Previous Version's Library

1. Configure Library Paths in RStudio
   - Direct RStudio to recognize the libraries from the previous version by setting the library paths manually. Enter the following command in RStudio's console:
     ```R
     .libPaths(c("/path/to/your/R_path/Versions/DESIRED_VERSION/Resources/library", .libPaths()))
     ```
     For example:
     ```R
     .libPaths(c("/Library/Frameworks/R.framework/Versions/4.3-x86_64/Resources/library", .libPaths()))
     ```

2. Persistent Configuration
   - To make this change persistent across all R sessions, modify the `.Rprofile` file in your home directory:
     ```R
     .First <- function() {
       .libPaths(c("/Library/Frameworks/R.framework/Versions/4.3-x86_64/Resources/library", .libPaths()))
       cat("Custom library path set.\n")
     }
     ```
   - Save the changes to `.Rprofile` and restart RStudio to apply them.

By following these steps, you ensure that RStudio can access and load library packages installed under previous versions, even after updating R to a newer version.

### Problem 3: R Dependency Package and gfortran Compatibility on Silicon Macs
Silicon Macs may encounter compatibility issues with R dependency packages that require the gfortran compiler, as the conventional GNU/Fortran compiler does not natively support Silicon Mac architecture.
**I acknowledge that this issue has been resolved automatically through a recent update (2022).**
#### Solution: Install a Compatible gfortran Version

1. Download and Install gfortran
   - Download a compatible version of `gfortran` for Silicon Mac from [mac.r-project.org/libs-arm64](https://mac.r-project.org/libs-arm64/).
   - Decompress the downloaded file and move it to the `/opt/R/arm64` directory.

2. Configure the R Environment
   - Locate the R environment configuration file:
     ```
     /Library/Frameworks/R.framework/Resources/etc/Makeconf
     ```
   - Open the `Makeconf` file and modify the Fortran compiler path to the newly installed gfortran:
     ```bash
     FC = /opt/R/arm64/gfortran/bin/gfortran -mtune=native
     FLIBS = -L/opt/R/arm64/gfortran/lib/gcc/aarch64-apple-darwin20.2.0/11.0.0 -L/opt/R/arm64/gfortran/lib -lgfortran -lemutls_w -lm
     ```
     **Note**: Intel Macs use the flags `-lgfortran -lemutls_w -lquadmath`, but Silicon Macs do not require `-lquadmath`.

#### Optional: Configure Homebrew for Silicon Mac

- If Homebrew is not yet installed on your Silicon Mac, or if you need to configure it for the Silicon architecture, follow these steps:

  ```bash
  nano ~/.zshrc
  # Add the following line to configure Homebrew:
  eval $(/opt/homebrew/bin/brew shellenv)
  ```

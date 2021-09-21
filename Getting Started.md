# Getting Started



## Set up your programming environnement Windows setup

### Necessary programs(**WAIT TILL STEP 2 TO INSTALL**):  
- VS Code or any other IDE you are comfortable with [VS Code](https://code.visualstudio.com/),
- GitBash (Git for Windows) [Gitbash](https://gitforwindows.org/),
- Ruby [Ruby](https://www.ruby-lang.org/en/),
- Python 3.7.9 **Important to have SAME stable release!!!** [Python](https://www.python.org/downloads/release/python-397/), 
- GCC for Windows (Use MinGw-w64) [Mingw-w64](https://www.mingw-w64.org/downloads/#mingw-builds),
- CMake (might not be necessary) [CMake](https://cmake.org/).

### Make sure your environnement path variable are properly set up.

For Ruby, after starting download make sure you : **Uncheck Run ridk install to setup MSYS2 and development toolchain**.

For Gitbash, no specific task to do.

For Python, before installing **make sure to delete any running instance of python and uninstall other versions of python**. 
Then check:
- System root C:\
- Make sure Python27 has been deleted
- C:\Program Files
- C:\Program Files (x86)
- %USERPROFILE%\AppData\Local\Microsoft\WindowsApps
- If Python is installed here, you must delete it manually
- %USERPROFILE%\AppData\Roaming\Python
- If Python is installed here, you must delete it manually
- Check your system path and remove any path to an old Python installation.
Then: 
- Got to Downloads and install Python
- Check Install launcher for all users
- Check Add Python 3.7 to PATH
- Choose Customize installation
- Check all optional features and click Next
- Check the following advanced options:
    - Install for all users
    - Associate files with Python
    - Create shortcuts for installed applications
    - Add Python to environment variables
    - Precompile standard library
- Change the install location to C:\Python37
- Click Install

For git: 
- Edit you system path
- Add C:\Program Files\Git\bin at the top of the system path

For GCC, follow instructions at [Website](https://techsupportwhale.com/install-gcc-compiler-on-windows/#:~:text=A%20Beginner%E2%80%99s%20Guide%20to%20Install%20gcc%20and%20g%2B%2B,Test%20installation%20of%20gcc%20and%20g%2B%2B%20compiler.%20) but **do not use their download link, use the link provided before and chose the Sourceforge link!!!**. Modern microcontrollers are built on 32 bits systems, make to install 32 bits version not just 64bits.

### Neccessary VS Code extensions:  
- C/C++ Extension Pack
- GitLens -- Git supercharged
- Git Graph

### How to setup Ceedling, Unity, CMock and CExeptions

Write follow command in gitbash (**After having setup MINGW64**):  gem install ceedling

Go to the Ceedling website [Ceedling](http://www.throwtheswitch.org/), find link to github repo to download Unity, CMock and CException from the github repo [Throw the switch](https://github.com/ThrowTheSwitch).

                             


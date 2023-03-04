---
layout: post
title: "Installing Lua on Windows"
date: 2023-03-04 02:26:10 -0600
categories: programming
description: >-
  building lua from source on windows using Cygwin, gcc, make
tags: [jekyll-blog, programming]
---

<style type='text/css'>#markdown-toc::before{content:'Table of Contents';font-weight:700}#markdown-toc{border:3px solid #aaa;padding:1.5em;margin-left:0;display:inline-block}</style>

* TOC
{:toc}

## Lua? Why?

It's been a while since I transitioned back to Windows (w11 specifically.) Since I possess only a single computer right now, I needed it to be able to do everything, which is mainly coding and music production. Since Windows has become a good all-rounder of an OS right now, making the switch back seems a no-brainer. Bonus points for increased battery life.

Having moved to windows, I missed Vim. I installed NeoVim to work with, but to write custom config files Lua was necessary.

Unfortunately, there is no direct binary to install. We need to build Lua from source.

Fortunately, there is [good documentation available for that.](http://lua-users.org/wiki/BuildingLuaInWindowsForNewbies)

## Prerequisites

- [7zip](https://www.7-zip.org/) (or any other equivalent)
- C/C++ Compiler (eg: gcc)
- Make (eg: GNUMake, mingw32-make)

## Cygwin

Cygwin has a large collection of GNU and Open Source tools which provide functionality similar to a Linux distribution on Windows.

Naturally, we can install `gcc` and `make` in Cygwin easily.

> [Download](https://www.cygwin.com/)  and Run the setup program, and add packages by searching and installing them.

![cygwin install packages gif](/assets/img/2023-03-04-cygwin-install-packages.gif)

## Install Lua

There is not binary for windows, so Lua needs to be built from source.

## Building Lua from source

In the [official documentation for building Lua on Windows](https://www.lua.org/download.html) they're using the [TDM-GCC](TDM-GCC) compiler.

Cygwin is already installed with `gcc` and `make` so there is no need for TDM-GCC.

### Download & Extract Lua Source Code

[Download source from the official website](https://www.lua.org/download.html) and extract it at `C:\lua` directory. Use **7zip**.

### Generate a build script

Using the official build script as the base, let's  modify the paths for `gcc` and `make` to the paths inside the Cygwin packages.

```bat
@echo off
:: ========================
:: file build.cmd
:: ========================
setlocal
:: you may change the following variable's value
:: to suit the downloaded version
set lua_version=5.4.4

set work_dir=%~dp0
:: Removes trailing backslash
:: to enhance readability in the following steps
set work_dir=%work_dir:~0,-1%
set lua_install_dir=%work_dir%\lua
set compiler_bin_dir=C:\cygwin64\bin\gcc.exe
set lua_build_dir=%work_dir%\lua-%lua_version%
set path=%compiler_bin_dir%;%path%

cd /D %lua_build_dir%
C:\cygwin64\bin\make.exe PLAT=mingw

echo.
echo **** COMPILATION TERMINATED ****
echo.
echo **** BUILDING BINARY DISTRIBUTION ****
echo.

:: create a clean "binary" installation
mkdir %lua_install_dir%
mkdir %lua_install_dir%\doc
mkdir %lua_install_dir%\bin
mkdir %lua_install_dir%\include

copy %lua_build_dir%\doc\*.* %lua_install_dir%\doc\*.*
copy %lua_build_dir%\src\*.exe %lua_install_dir%\bin\*.*
copy %lua_build_dir%\src\*.dll %lua_install_dir%\bin\*.*
copy %lua_build_dir%\src\luaconf.h %lua_install_dir%\include\*.*
copy %lua_build_dir%\src\lua.h %lua_install_dir%\include\*.*
copy %lua_build_dir%\src\lualib.h %lua_install_dir%\include\*.*
copy %lua_build_dir%\src\lauxlib.h %lua_install_dir%\include\*.*
copy %lua_build_dir%\src\lua.hpp %lua_install_dir%\include\*.*

echo.
echo **** BINARY DISTRIBUTION BUILT ****
echo.

%lua_install_dir%\bin\lua.exe -e"print [[Hello!]];print[[Simple Lua test successful!!!]]"

echo.

pause

```

Take care to put the correct Cygwin paths for `gcc` and `make` in the  **above lines**:

```bat
set compiler_bin_dir=C:\cygwin64\bin\gcc.exe
```

```bat
C:\cygwin64\bin\make.exe PLAT=mingw
```

Save as `build.cmd` inside `C:\lua` and run. This will build Lua.

## Finishing up

The build should be complete without any issues. After the build is done, add `C:\lua\bin` to the path.

Logout & Login for the env variables to take effect.

Test if lua is working.

![check lua working in pwsh](/assets/img/2023-03-04-lua-pwsh-check-running.gif)

Next is to setup NeoVim to my taste.

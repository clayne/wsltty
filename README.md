Mintty as a terminal for WSL (Windows Subsystem for Linux).

<img align=right src=wsltty.png>

### Overview ###

WSLtty components
* wsltty package components (see below) in the user’s local application folder 
  `%LOCALAPPDATA%`
* a wsltty configuration directory in the user’s application folder `%APPDATA%`
  (“home”-located configuration files from a previously installed version 
  will be migrated to the new default location)
* Start Menu shortcuts to start WSL terminals
* Desktop shorcut to start a terminal for the default WSL distribution
* `*.bat` scripts to invoke WSL terminals from the command line
* optional context menu entries for Windows Explorer to start WSL terminals in the respective folder
* install/uninstall context menu items from Start Menu subfolder `WSLtty`

---

### Requirements ###

To connect to WSL, wsltty uses wslbridge2, which uses undocumented 
Windows APIs that have been changed various times, so wslbridge2 needed 
to catch up with incompatible changes, particularly to support WSL V2.
(See e.g. issue #343; to work with WSL V2, wsltty 2.0.0 needed a WSL update 
to [release 1.3.17](https://github.com/microsoft/WSL/releases/tag/1.3.17).)

Since release 3.0.5, WSLtty requires Windows version 1809 (the November 2018 release).

By end of 2024, wsltty works again with recent updates of the WSL subsystem.

---

### Installation from this repository ###

#### WSLtty installer ([Download](https://github.com/mintty/wsltty/releases) standalone installation) ####

From the [release downloads](https://github.com/mintty/wsltty/releases), 
run the wsltty-VERSION-x86_64-install.exe installer to install 
the components listed above. Make sure to select a 64-bit installer 
on a 64-bit system.
If Windows complains with a “Windows protected your PC” popup, 
you may need to click “Run anyway” to proceed with the installation.
You may need to open the Properties of the installer first, tab “General” 
section “Security” (if available) and select “Unblock”, 
to enable the “Run anyway” button.

#### WSLtty Portable installer

For a portable installation, e.g. on a USB stick, choose the 
“-install-portable.exe” file for download. Installation will prompt 
for a portable installation folder interactively. 
For example, choosing `U:\opt` will create and use folder 
`U:\opt\wsltty` both as installation directory and configuration directory.
Portable installation does not install any start menu or desktop shortcuts 
and no context menu entries. It creates a shortcut in the selected 
portable installation folder to start the default WSL distribution.

Note: For an update installation, either the parent directory or the target 
directory itself can be selected.

Note: If you rename or move the installation directory, the icon of the 
“WSL Terminal Portable” shortcut will not work anymore; re-run the 
install-portable.bat script in the installation folder to refresh it.

#### Installation from archive ####

In case a local anti-virus guard barfs about the wsltty installer, the 
release also contains a `.cab` file. Download it, open it, extract its files 
to some temporary deployment directory, and invoke `install.bat` from there, 
or `install-portable.bat` for a portable installation.

#### Quiet installer ####

The wsltty-VERSION-x86_64-install-quiet.exe installer is intended for 
integration in another installation framework.

#### Installation from source repository ####

Checkout the wsltty repository, or download the source archive, unpack and rename the directory to `wsltty`.
Install Alpine WSL from the Microsoft Store.
Invoke `make build`, then `make install`.

Note this has to be done within a Cygwin environment. A minimal Cygwin 
environment for this purpose would be installed with the 
[Cygwin installer](https://cygwin.com/setup-x86_64.exe) 
from [cygwin.com](https://cygwin.com/), 
with additional packages `make`, `gcc-g++`, `unzip`, `zoo`, `patch`, (`lcab`).

#### Build installers ####

Install a minimal Cygwin environment plus the additional packages as 
listed for «Installation from source repository».
Invoke `make pkg` or just `make`.

#### Installation to non-default locations ####

(For experts)
Within the installation process, provide parameters to the script `install.bat`.
The optional first parameter designates the installation target,
the optional second parameter designates the configuration directory.

### Installation with other package management environments ###

Note: These are 3rd-party packages, not managed by this repository.

#### Windows Package Manager ####

([Check package](https://github.com/microsoft/winget-pkgs/tree/master/manifests/m/Mintty/WSLtty))
To install wsltty from the 
[Windows Package Manager Community Repository](https://github.com/microsoft/winget-pkgs), 
invoke one of
* `winget install wsltty`
* `winget upgrade wsltty`

#### Chocolatey ####

([Check package](https://community.chocolatey.org/packages/wsltty))
If you use the [Chocolatey package manager](https://chocolatey.org/), 
invoke one of
* `choco install wsltty`
* `choco upgrade wsltty`

#### Scoop ####

([Check package](https://scoop.sh/#/apps?q=wsltty))
If you use the [Scoop package manager](https://scoop.sh/), 
* `scoop bucket add extras`

then, invoke one of
* `scoop install wsltty`
* `scoop update wsltty`

### Uninstallation ###

To uninstall wsltty desktop, start menu, and context menu integration:
Open a Windows `cmd`, go into the wsltty installation folder:
`cd %LOCALAPPDATA%\wsltty` and run the `uninstall` script.
To uninstall wsltty software completely, remove the installation folder manually.

---

### Invocation ###

WSLtty can be invoked with
* installed Start Menu shortcuts (or Desktop shortcuts if copied there)
* *.bat scripts (optionally with WSL command as parameters) (see [Command line scripts](#command-line-scripts-wslbat) below)
* Explorer context menu (if installed from the Start Menu `WSLtty` subfolder)

Starting the mintty terminal directly from the WSLtty installation location 
is discouraged because that would bypass essential options.

#### WSL V2 ####

Terminal communication with WSL via its modes V1 or V2 is handled 
automatically by wsltty (mintty and the wslbridge2 gateway).

#### Starting issues ####

If wsltty fails with an 
`Error: Could not fork child process: Resource temporarily unavailable`...,
its runtime may be affected by some over-ambitious virus defense strategy.
For example, with Windows Defender, option “Force randomization for images” 
should be disabled.

If wsltty fails with an error message that mentions a disk mount path (e.g. `/mnt/c`),
workarounds may be the shutdown of the WSL V2 virtual machine (`wsl --shutdown` on the distro) 
or turning off “fast startup” in the Windows power settings (#246, #248).

#### WSL shell starting issues ####

With WSL V2, an additional background shell is run which may cause trouble 
for example when setting up automatic interaction between Windows side and 
WSL side 
(see https://github.com/mintty/wsltty/issues/197#issuecomment-687030527).
As a workaround, the following may be added to (the beginning of) the 
WSL shell initialization script `.bashrc` (adapt for other shells):
```
    # work around https://github.com/mintty/wsltty/issues/197
    if [[ -n "$WSL_DISTRO_NAME" ]]; then
      command -v cmd.exe > /dev/null || exit
    fi
```

---

### Configuration ###

#### Start Menu and Desktop shortcuts ####

In the Start Menu, the following shortcuts are installed:
* Shortcut <img align=absmiddle height=40 src=tux1.png>`WSL Terminal` to start the default WSL distribution (as configured with the Windows tool `wslconfig` or `wsl -s`)
* For each installed WSL distribution, for example `Ubuntu`, a shortcut like <img align=absmiddle height=40 src=ubuntu1.png>`Ubuntu Terminal` to start in the WSL user home

In the Start Menu subfolder WSLtty, the following additional shortcuts are installed:
* Shortcut <img align=absmiddle height=40 src=tux1.png>`WSL Terminal %` to start the default WSL distribution in the Windows %USERPROFILE% home
* For each installed WSL distribution, for example `Ubuntu`, a shortcut like <img align=absmiddle height=40 src=ubuntu1.png>`Ubuntu Terminal %` to start in the Windows %USERPROFILE% home

One Desktop shortcut is installed:
* Shortcut <img align=absmiddle height=40 src=tux1.png>`WSL Terminal` to start the default WSL distribution (as configured with the Windows tool `wslconfig` or `wsl -s`)

Other, distribution-specific shortcuts can be copied to the desktop 
from the Start Menu if desired.

The Start menu folder WSLtty contains the link 
<img align=absmiddle height=25 src=https://user-images.githubusercontent.com/12740416/57078483-a7846a00-6cee-11e9-9c5e-8c2e9e56cae4.png>`configure WSL shortcuts`.
This function is initially run when wsltty is installed.
It should be rerun after adding or removing WSL distributions, 
in order to create the respective set of shortcuts in the Start menu.

#### Command line scripts `wsl*.bat` ####

WSLtty installs the following scripts into `%LOCALAPPDATA%\Microsoft\WindowsApps` 
(and a copy in its application folder `%LOCALAPPDATA%\wsltty`):

* For each installed WSL distribution, e.g. Ubuntu, a command script like `Ubuntu.bat` to start in the current folder/directory
* For each installed WSL distribution, e.g. Ubuntu, a command script like `Ubuntu~.bat` to start in the WSL user home
* `WSL.bat` and `WSL~.bat` to start the default WSL distribution

The scripts accept an optional invocation command (since 3.7.8).

Given that `%LOCALAPPDATA%\Microsoft\WindowsApps` is in your PATH,
the scripts can be invoked from cmd.exe, PowerShell, or via WIN+R.

#### Context menu entries ####

WSLtty provides context menu entries for all installed WSL distributions 
and one for the configured default distribution,
to start a respective WSL terminal in a specific folder from an Explorer window.
They are not installed by default.

To add launch entries for the default or all WSL distributions to the 
Explorer context menu, or remove them, run the respective script from the 
Start Menu subfolder `WSLtty`:
* <img align=absmiddle height=25 src=https://user-images.githubusercontent.com/12740416/57078483-a7846a00-6cee-11e9-9c5e-8c2e9e56cae4.png>`add default to context menu`
  adds context menu entries for the default WSL distribution
* <img align=absmiddle height=25 src=https://user-images.githubusercontent.com/12740416/57078483-a7846a00-6cee-11e9-9c5e-8c2e9e56cae4.png>`add to context menu`
  adds context menu entries for all WSL distributions
* <img align=absmiddle height=25 src=https://user-images.githubusercontent.com/12740416/57078483-a7846a00-6cee-11e9-9c5e-8c2e9e56cae4.png>`remove from context menu`
  removes context menu entries for WSL distributions

#### Icon ####

Wsltty installation and the mintty terminal try to use the icon of the 
respective WSL distribution. If it cannot be determined, a penguin icon 
is used as a fallback. You can replace it with your preferred default icon 
by replacing the icon file `%LOCALAPPDATA%\wsltty\wsl.ico`.

#### Mintty settings ####

Mintty can maintain its configuration file in various locations, 
with the following precedence:
* file given with mintty option `-c` (not used by wsltty default installation)
* file `config` in directory given with mintty option `--configdir`
  * **`%APPDATA%\wsltty\config`** in the default wsltty installation
* `%HOME%\.minttyrc` (usage deprecated with wsltty)
* `%HOME%\.config\mintty\config` (usage deprecated with wsltty)
* common config file for all mintty installation instances
  * **`%APPDATA%\mintty\config`**
* `%LOCALAPPDATA%\wsltty\etc\minttyrc` (usage deprecated with wsltty)

Note:
* `%APPDATA%\wsltty\config` is the user configuration file location. 
  Further subdirectories of `%APPDATA%\wsltty` are used for language, 
  themes, and sounds resource configuration. 
  Note the distinction from `%LOCALAPPDATA%\wsltty` which is the default 
  wsltty software installation location.
* The `%APPDATA%\mintty\config` option provides the possibility to 
  maintain common mintty settings for various installations (like 
  wsltty, Cygwin, MinGW/msys, Git for Windows, MinEd for Windows).
* (About deprecated options) By default, `%HOME%` would refer to the 
  root directory of the cygwin standalone installation hosting wsltty. 
  So `%HOME%` would mean `%LOCALAPPDATA%\wsltty\home\%USERNAME%`.
  If you define `HOME` at Windows level, this changes accordingly.
  Note, however, that the WSL `$HOME` is a completely different setting.

#### Emoji deployment ####

Mintty and the wsltty package do not bundle actual emoji graphics but 
there are scripts to support easy download and deployment.
If you have another instance of mintty installed (e.g. in cygwin) 
and have emojis deployed already in the common config folder 
`%APPDATA%\mintty\emojis`, they will be reused by wsltty.

To deploy emojis standalone for wsltty, use the scripts installed in 
`%APPDATA%\wsltty\emojis` within WSL:
* `cd $(wslpath "$APPDATA/wsltty/emojis")`
* `getemojis` to provide emoji graphics as listed by Unicode.org
* `getflags` to provide emoji flag graphics (extending Unicode dynamically) from various sources

#### Shell selection and Login shell ####

The WSLtty deployment does not impose a shell preference;
it invokes the user’s default shell in login mode by the final `-` parameter:
* `%LOCALAPPDATA%\wsltty\bin\mintty.exe --WSL= --configdir="%APPDATA%\wsltty" -`

You may tweak shortcuts, scripts, or context menu entries as follows:

To launch a default shell in non-login mode, remove the final dash.

To invoke your preferred shell, replace the final dash with 
a shell pathname and an optional `-l` parameter 
* `%LOCALAPPDATA%\wsltty\bin\mintty.exe --WSL= --configdir="%APPDATA%\wsltty" /bin/bash -l`

---

### WSL locale setup and character encoding ###

Character encoding setup by locale setting is propagated from the terminal 
towards WSL. So you can select your favourite locale with configuration 
options or with command-line options, for example in a copied dedicated 
desktop shortcut.

If for example you wish to run WSL in GB18030 encoding, you may set options
`Locale=zh_CN` and `Charset=GB18030` and the WSL shell will adopt that 
setting, provided that the selected locale is configured to be available 
in the locale database of the WSL distribution.
This can be achieved in Ubuntu with the following commands:
* `sudo mkdir -p /var/lib/locales/supported.d`
* `sudo echo zh_CN.GB18030 GB18030 >> /var/lib/locales/supported.d/local`
* `sudo locale-gen`

---

### Components and Credits ###

For mintty, see the [Mintty homepage](http://mintty.github.io/) 
(with further screenshots), 
the [Mintty manual page](http://mintty.github.io/mintty.1.html), 
<br>and the [Mintty Wiki](https://github.com/mintty/mintty/wiki), 
including a [Hints and Tips page](https://github.com/mintty/mintty/wiki/Tips).

It is based on [Cygwin](http://cygwin.com) 
and includes its runtime library ([sources](http://mirrors.dotsrc.org/cygwin/x86/release/cygwin)).

For interacting with WSL, [wslbridge](https://github.com/rprichard/wslbridge)
used to be the gateway prototype.
Many thanks for this enabling gateway go to Ryan Prichard.

For recent changes in WSL, particularly WSL mode V2, the new gateway 
[wslbridge2](https://github.com/Biswa96/wslbridge2) is used instead.
Many thanks for this further development and maintenance go to Biswapriyo Nath.


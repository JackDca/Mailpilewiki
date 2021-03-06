![Guide](https://github.com/pagekite/Mailpile/wiki/images/page-guide.png)

# Installing the Mailpile Beta on OS X

1. Please download the OS X installer from [our website](https://www.mailpile.is/download/).

2. Once downloaded, open the installer from your Downloads directory. When the Mailpile folder opens, drag the Mailpile.app application icon into your Applications directory.

    ![Install Mailpile](https://github.com/haraldnagel/Mailpile-OS-X-screenshots/blob/master/0.4.0/Mailpile-dmg.png)

3. Now, launch Mailpile by navigating to your Applications directory or selecting it in Launchpad.

4. You may see a Gatekeeper dialog box shown below if you have not changed the default security settings.

    ![Gatekeeper alert](https://github.com/haraldnagel/Mailpile-OS-X-screenshots/blob/master/0.4.0/Gatekeeper-warning.png)

   In order to run Mailpile you can temporarily alter the default Gatekeeper security settings in the Security & Privacy System Preferences pane (see Apple’s [Gatekeeper documentation](http://support.apple.com/kb/HT5290) for more information), but the much easier way is to just right-click the icon, select “Open” from the contextual menu and confirm you want to launch the application (which may require admin authorization). In either case, after you started Mailpile for the first time, you can simply launch it as usual.
    
5. At this point you should be able to follow the Mailpile configuration wizard.

6. Mailpile also provides a convenient menu bar icon for quick access.

    ![Mailpile menu bar icon](https://github.com/haraldnagel/Mailpile-OS-X-screenshots/blob/master/0.4.0/Mailpile-menu.png)

**This documentation needs expanding.**

# Tips for developers using MacOS

If you want to help develop Mailpile by submitting pull requests, then some of these instructions might still be relevant. We will work to update this procedure as soon as possible!

### Leopard 10.6.8
Mailpile depends on lxml, which is a binder for two C libraries (libxml2 and libxslt). The GCC compiler needs to be installed on your os for those dependencies to work but is unfortunately not included with MacOS 10.6.8.

Some people are using XCode (that comes with GCC), others skip downloading the massive XCode installer and use [GCC packaged by Kenneth Reitz](https://github.com/kennethreitz/osx-gcc-installer). Long story short, to install Mailpile you have to have the GCC compiler installed, however you do it, and install lxml when GCC has been successfully installed. You will find [several discussion](http://stackoverflow.com/questions/1277124/how-do-you-install-lxml-on-os-x-leopard-without-using-macports-or-fink) threads regarding this issue if you run into any problems.
  
  
### Mavericks 10.9.1 (Step-by-Step)  
Download and Install Xcode Command Line Tools:   
```
xcode-select --install  
```


Download and Install PIP for Python:  
```
cd /tmp  
curl -L -o get-pip.py https://raw.github.com/pypa/pip/master/contrib/get-pip.py  
sudo python get-pip.py  
```


Install latest version of ‘lxml’ using Static Dependencies:  
```
sudo STATIC_DEPS=true pip install lxml  
```

OR install 'lxml' without pip, macports, or fink
```
cd /tmp  
curl -o lxml-3.3.0.tgz http://lxml.de/files/lxml-3.3.0.tgz  
tar -xzvf lxml-3.3.0.tgz  
cd lxml-3.3.0  
python setup.py build --static-deps --libxml2-version=2.8.0  --libxslt-version=1.1.24  
sudo python setup.py install
```

Clone and Install Mailpile (in user 'Sites' directory, but any directory will do):  
```
cd ~/Sites  
git clone -b release/alpha https://github.com/pagekite/Mailpile.git  
cd Mailpile  
sudo pip install -r requirements.txt  
```

If you run into an error like
```
    clang: warning: unknown argument: '-mno-fused-madd' [-Wunused-command-line-argument-hard-error-in-future]
```
it is because you have a newer version of the Apple LLVM compiler in Xcode 5.1 that is stricter about flags, so prefix your build commands with this:

```
ARCHFLAGS=-Wno-error=unused-command-line-argument-hard-error-in-future 
```

e.g., 

```
ARCHFLAGS=-Wno-error=unused-command-line-argument-hard-error-in-future  python setup.py build --static-deps --libxml2-version=2.8.0  --libxslt-version=1.1.24  13ARCHFLAGS=-Wno-error=unused-command-line-argument-hard-error-in-future  python setup.py build --static-deps --libxml2-version=2.8.0  --libxslt-version=1.1.24  
```

and

```
sudo ARCHFLAGS=-Wno-error=unused-command-line-argument-hard-error-in-future  pip install -r requirements.txt
```


[**UGLY HACK WARNING** - Mailpile calls `python2` when opening, but in OS X this binary/symbolic link does not exist, instead we call `python` or `python2.X` directly.  For this reason, we must accommodate Mailpile by creating a symbolic link in the `usr/bin/` directory ]

Create symbolic link from 'python2.7' to 'python2':  
```
sudo ln -s /usr/bin/python2.7 /usr/bin/python2  
```


Finally - run Mailpile!  
```
./mp  
```

**See "Getting started on linux" for more information on setting up Mailpile for the first time.**
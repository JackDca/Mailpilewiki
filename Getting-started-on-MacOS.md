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
curl -o get-pip.py https://raw.github.com/pypa/pip/master/contrib/get-pip.py  
sudo python get-pip.py  
```


Install latest version of ‘lxml’ using Static Dependancies:  
```
sudo STATIC_DEPS=true pip install lxml  
```


Clone and Install Mailpile (in user 'Sites' directory, but any directory will do):  
```
cd ~/Sites  
git clone -b release/alpha https://github.com/pagekite/Mailpile.git  
cd Mailpile  
sudo pip install -r requirements.txt  
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
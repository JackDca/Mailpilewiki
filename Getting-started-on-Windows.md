![Guide](https://github.com/pagekite/Mailpile/wiki/images/page-guide.png)

Please download the Windows installer from [our website](https://www.mailpile.is/download/). 

**This documentation needs expanding.**

# Tips for developers using Windows

If you want to help develop Mailpile by submitting pull requests that would be awesome. We need to create documentation about how to best setup a dev version of Mailpile in Windows.

Download links used to resolve dependencies:

    python 2.7: <http://www.python.org/download/>
    lxml: <http://www.lfd.uci.edu/~gohlke/pythonlibs/#lxml>
    pyreadline: <https://pypi.python.org/pypi/pyreadline/2.0>

2. Clone the source repository

# clone Mailpile, docs and plugins (submodules) to your machine
git clone --recursive https://github.com/mailpile/Mailpile.git

This will clone the main development branch of Mailpile. If you want to clone a specific branch, specify it like so:

git clone --recursive -b branchname https://github.com/mailpile/Mailpile.git

A full list of existing branches is available on Github. Generally you'll want the highest version number available.

3. Setup your virtual environment

# move into the newly created source repo
cd Mailpile

# create a virtual environment directory
virtualenv -p "C:\Python27\python.exe" --system-site-packages mp-virtualenv

# activate the virtual Python environment
"mp-virtualenv\Scripts\activate"

What is virtualenv?

Virtualenv is a tool which allows you to install the Mailpile's Python dependencies locally, without having to modify your operating system's global Python. This keeps things contained and makes it easier to un-install everything all at once. The downside, is you need to activate the virtual environment before you continue setting up Mailpile and always before you run Mailpile. That is what the source command does.

4. Install the dependencies

Important: You must have activated the virtual Python environment in your current shell, as described in step 1 above. If you opened a new shell or a new terminal window, see section 5.1.

If you want to develop Mailpile:

python -m pip install -r requirements-dev.txt

For production or end-users, install everything that's listed in requirements.txt instead.

python -m pip install -r requirements.txt

Also see if pyreadline is installed else run the following command

python -m pip install pyreadline

If all is well, you should now be able to run Mailpile.

5. Start Running Mailpile

You can now run Mailpile directly using the command:

./mp



There is a windows-equivalent to "mp" in the root directory. This should
match the behavior of the existing "mp" assuming the user installed python
2.7 in the default installation location.


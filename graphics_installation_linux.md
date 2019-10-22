# graphics.h installation in Ubuntu 18.04

The graphics.h library doesn't come pre-installed in gcc by default in Ubuntu and while it is a straightforward process of installation, there can be certain problems that can be faced. These are the basic steps steps that you need to follow:
## Enable the universe repo

> `sudo add-apt-repository universe`

> `sudo apt update`

## Install the basic compilers

> `sudo apt install build-essential`

## Additional packages for graphics processing

>`sudo echo "deb http://us.archive.ubuntu.com/ubuntu/ xenial main universe" >> /etc/apt/sources.list`

>`sudo echo "deb-src http://us.archive.ubuntu.com/ubuntu/ xenial main universe" >> /etc/apt/sources.list
sudo apt update`

This part may face problems as `echo` is run as root, but not `>>`. This works better:

>`echo "deb http://us.archive.ubuntu.com/ubuntu/ xenial main universe" | sudo tee -a /etc/apt/sources.list`

>`echo "deb-src http://us.archive.ubuntu.com/ubuntu/ xenial main universe" | sudo tee -a /etc/apt/sources.list`

Then installing all the additional packages:
>`sudo apt install libsdl-image1.2 libsdl-image1.2-dev guile-2.0 guile-2.0-dev libsdl1.2debian libart-2.0-dev libaudiofile-dev libesd0-dev libdirectfb-dev libdirectfb-extra libfreetype6-dev libxext-dev x11proto-xext-dev libfreetype6 libaa1 libaa1-dev libslang2-dev libasound2 libasound2-dev`

In my situation, the `libguile.h` file was not found in when running the `make` command (which happens later). Due to a system bug, it didn't find it in the standard paths, so adding its include path to preprocessor and linker flags seemed to do the trick for me:

>`CPPFLAGS="$CPPFLAGS $(pkg-config --cflags-only-I guile-2.0)"`
>
>`CFLAGS="$CFLAGS $(pkg-config --cflags-only-other guile-2.0)"`
>
>`LDFLAGS="$LDFLAGS $(pkg-config --libs guile-2.0)"`

## Download and install libgraph
>`wget http://download.savannah.gnu.org/releases/libgraph/libgraph-1.0.2.tar.gz`

>`tar zxf libgraph-1.0.2.tar.gz`

Go into the directory where libgraph is extracted using `cd` and install libgraph using the following commands:

>`./configure`

>`make`

>`sudo make install`

>`sudo cp /usr/local/lib/libgraph.* /usr/lib`

Now, graphics.h can be used by using the following lines in the C++ code:
```
int gd=DETECT,gm; <br>
initgraph(&gd,&gm,NULL);
```

## Compiling and running C++ file in gcc

There are two methods to running a cpp file using gcc and when using libgraph, `-lgraph` has to be added after the gcc command.

### Method 1

>`sudo gcc first.cpp -lgraph`

For this after compilation, the executable file is by default `a.out`. Running this, runs the graphic program.

>`./a.out`

### Method 2

>`sudo gcc -o first first.cpp -lgraph`

Here we set a name for the compiled output file as `first`. This will run the program in this situation

>`./first`
# Configuring the Toolflow

It is now time to setup the Xilinx, Matlab and work paths to suit your directory setup. This tells the toolflow where you have installed MATLAB and Xilinx tools, so that it can find and run them. The configuration will depend on the options you selected when you installed these tools.

The design entry portion of the CASPER tool flow consists of MATLAB, Simulink,and System Generator.  The build portion of the CASPER tool flow is done using Xilinx Platform Studio (XPS) or Vivado, depending on the target hardware.  All of these tools require various environment variables to be set.  The `startsg` script will handle all the requisite setup details.

## The `startsg` script

The `startsg` script operates in two different modes, depending on how it is invoked.  When executed as a script it will setup the environment suitably and then launch Matlab (for design entry).  When "sourced" using the bash `source` command, it will setup the environment of the current shell to allow command line use of the Xilinx tools and various CASPER scripts.

    # Running startsg to start Matlab
    $ /path/to/mlib_devel/startsg

    # Sourcing startsg to setup for command line tools
    # and then running exec_flow.py from the command line
    $ source /path/to/mlib_devel/startsg
    $ /path/to/mlib_devel/jasper_library/exec_flow.py ...
    

## Specifying local details

The `startsg` script is generic.  It does not require that the Matlab and Xilinx tools be installed in specific locations, but it does require that you provide it with a few details about your local installation.  This is done by creating a `startsg.local` file that defines a few key variables from which the other environment variables can be derived.  The two absolutely essential variables are `MATLAB_PATH` and `XILINX_PATH`.  Another variable that can be defined is `PLATFORM`, which is used by the Xilinx tools to select suitable runtime binaries for your system.  If not specified, it will be defaulted to `lin64` which (as of this writing) is the most commonly used platform for CASPER development and a warning message will be issued.  Other variables that you may wish to define in `startsg.local` are `XILINXD_LICENSE_FILE` to point to your Xilinx software license if it exists in a non-standard location and `JASPER_BACKEND` to specify which Xilinx tools will be used to implement your design (currently supported options are `vivado` or `ise`, with `vivado` being the default).

Here is a sample `startsg.local` file:

    export XILINX_PATH=/opt/Xilinx/Vivado/2016.4
    export MATLAB_PATH=/usr/local/MATLAB/R2016b
    export PLATFORM=lin64
    export JASPER_BACKEND=vivado

## Other features

### Symlink for convenience

Running `startsg` from the `mlib_devel` directory (where it lives) will start Matlab with `mlib_devel` as the current directory.  This requires that you navigate within Matlab to the directory where your model file lives.  To avoid this minor annoyance, you can create a symbolic link to `startsg` in your application directory (i.e.  where your model file lives).  When running `startsg` via this symlink, Matlab will start up with your application directory as the current directory and also run the optional `casper_startup.m` file if one exists.

### Symlinks for multiple tool versions

If you have multiple versions of the Matlab or Xilinx tools, you can create different `startsg.local` files for the different versions.  For example, you might have `startsg-2016-1.local` that points to the 2016.1 version of the Xilinx tools and `startsg-2016-4.local` that points to the 2016.4 version.  To utilize these version specific `.local` files, you can either pass them on the command line to `startsg` or you can create symlinks with version specific names that match the base name of the version specific local file.  Here are some examples that show how this works:

    $ startsg                       # Uses startsg.local

    $ startsg startsg-2016-1.local  # Uses startsg-2016-1.local

    $ ln -s /path/to/mlib_devel/startsg startsg-2016-1
    $ ./startsg-2016-1              # Uses startsg-2016-1.local

Now copy 'startsg.local.example' to 'startsg.local' and add the correct path to the following parameters in 'startsg.local': `XILINX_PATH` (path to your Xilinx script), `MATLAB_PATH` (path to your Matlab script), `PLATFORM` (this is set to `lin64` for a 64 bit Linux system) and `JASPER_BACKEND` (this is set to `vivado` by default, but can also be `ise`). Remember to save the file. NB: Please do not commit or push these file changes to the git repo, as this file will constantly change from one user to the next. In fact, the repository has been explicitly configured to ignore changes to the `startsg.local` file -- you shouldn't change this behaviour!
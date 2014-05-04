freshMRI
==========

HowTo set up up a neuroimaging data analysis environment in OS X Mavericks using [Homebrew](brew.sh).
wem3@uoregon.edu
updated: 20140501
This guide borrows liberally from [stowler's setupNeuroimagingEnvironment.md](https://github.com/stowler/brainwhere/blob/master/docs/setupNeuroimagingEnvironment.md).  


There are some truly excellent neuroimaging tools that are widely available and easy to use. Many neuroimagers are not (yet) computer scientists, however, and installing different software packages can be a bit of a challenge. This document is intended to make it a little bit easier to set up a Mac for neuroimaging data analysis.

If you're already a whiz-bang programmer, the pace of this guide may be a little slow, and if you do all of your neuroimaging data analysis remotely on a university super-computer, then someone else may manage the installation and maintenance of these sorts of tools for you. If, however, you want to set up a gnarly machine to crunch data locally, or even just install these programs on your laptop so that you can look at results or build jobs, this guide might help. It assumes that you have a fresh working install of Mac OS X Mavericks (10.9) and an administrator account on the computer (which may be necessary for some tools, although we try to avoid gratuitous sudo when possible). 

Xcode
==================

In order to build some of the tools we'll need (including Homebrew), we need to have the Command Line Tools from [Xcode](https://itunes.apple.com/us/app/xcode/id497799835?mt=12) installed. You can install X-Code from the Mac App Store, but if you want to save space on your hard drive (it's a big application), you can install just the Command Line Tools. To do so, open up Terminal.App (in /Applications/Utilities), or whatever your favorite terminal is.  

At the command prompt, type:  
```
xcode-select --install
```  
(and press 'Enter')
*Note: all future code blocks assume that you press 'Enter' to execute the code*  

A pop-up window will ask if you would like to install the tools. Select 'Install' ([here is a step-by-step guide with screenshots](http://www.computersnyou.com/2025/2013/06/install-command-line-tools-in-osx-10-9-mavericks-how-to/)).


Homebrew
==================

Homebrew is a package manager. It helps you to install pieces of software that don't come with OS X by default, and also helps to make sure that none of those programs cause messy interactions with files your system needs to run properly. Hombrew Casks can install applications that aren't necessarily hard to install via drag n' drop, but with the added conveniences of easy updates, uninstallation, and organization.

To install Homebrew, open up Terminal.App (in /Applications/Utilities), or whatever your favorite terminal is.

At the command prompt, paste:
```
ruby -e "$(curl -fsSL https://raw.github.com/Homebrew/homebrew/go/install)"  
```

Give your system a quick check-up, and run:  
```
brew doctor
```  
If everything went smoothly, it should read:  
```
system is ready to brew
```  

The doctor will offer helpful suggestions if anything is amiss, and suggest that you post a bug report if it doesn't have an answer for you.  

You can update Homebrew itself with:  
```
brew update
```

or upgrade all packages installed with:  
```
brew upgrade
```  
Things move pretty fast in brewworld, so update frequently.  

Let's use Homebrew to actually install something... the capability to use Homebrew Casks:
```
brew tap caskroom/cask  
brew install brew-cask
```

Now we can use homebrew-cask to install [XQuartz](http://xquartz.macosforge.org). The GUI interfaces for a number of imaging tools require an X-11 windowing system (which is what XQuartz is), and if you interact with a cluster remotely, you probably use a similar interface.
```
brew cask install xquartz
```
Before XQuartz will work, you need to log out and log back in (although if you want to be thorough, a hard reboot never hurts).

If you don't spend a lot of time in the world of command line interfaces, this may seem like an awful lot of work to install XQuartz, which you could have done from a browser window in about 30 seconds. What's more, it isn't entirely clear you even *need* XQuartz.  This is actually one of the things that's so great about Homebrew proper: it manages dependencies *for* you. A lot of neuroimaging software isn't entirely self-contained like YourFavortieApplication.App, and probably depends on other low-level system files that live the dark recesses of your computer and can be difficult to access through a Finder window (and are sometimes even entirely hidden). 

FSL, for example, installs by default into /usr/local. Once you start setting up shop in /usr/local and the like, if you don't keep track of permissions and bindings and paths. Letting Homebrew help manage these sorts of issues can make your life a whole lot easier, and also allow you to do hardcore data analysis while keeping the "it just works" functionality of your Mac in tact.

If we could install *everything* through Homebrew, life would be a lot easier. Another 


FSL
==================

Install FSL on Mac OS X Mavericks:
----------------------------------------------------------------

[FSL's online instructions](http://fsl.fmrib.ox.ac.uk/fsl/fslwiki/FslInstallation/MacOsX#Downloading_the_install_file_without_installing_the_software) are pretty comprehensive, and there are a few ways to install it, but this is one way of installing and testing FSL that generally works for me:


There are multiple ways to install (as of May 2013). I get mixed results with the recommended fslinstaller.py, but I typically use it and then just fix whatever didn't work.

1. Run FSL's installer script in  "download only" mode (about 1-hr):

    ```
    cd ~/Downloads
    python fslinstaller.py -o
    ```

2. Calculate the md5 sum of the downloaded file and use it to launch installation:

    ```
    fslDownload=fsl-5.0.2.2-macosx.tar.gz
    fslDestDir="/usr/local"
    fslMD5=`md5 ${fslDownload} | awk '{ print $NF}'` 
    python fslinstaller.py -d ${fslDestDir} -f ${fslDownload} -C ${fslMD5}
    ```

3. After the install completes, confirm that the file /etc/bashrc received a block of FSL environmental variables (below). If not, the install program may have added it to your personal ~/.profile or ~/.bash_profile instead. For system-wide installation, remove from those files and append to /etc/bashrc . Do this by first caching your sudo credentials via the command `sudo tail /var/log/auth.log`  (ignore the output from this command, it is just an excluse to give your password to sudo). Then immediately copy this block of lines and paste it into the terminal:


    ```
    #    WARNING: note the \${escapedVariables} below, which
    #    are escaped for heredoc (http://goo.gl/j3HMJ). 
    #    Un-escape them if manually typing into a text editor.
    #    Otherwise, just paste these lines to your bash prompt
    #    (up to and including "EOF" line):
    #
    editDate=`/bin/date +%Y%m%d`
    editTime=$(date +%k%M)
    sudo tee -a /etc/bashrc >/dev/null <<EOF
    #------------------------------------------
    # on ${editDate} at ${editTime}, user $USER 
    # added some FSL setup:
    FSLDIR=/usr/local/fsl
    PATH=\${FSLDIR}/bin:\${PATH}
    export FSLDIR PATH
    . \${FSLDIR}/etc/fslconf/fsl.sh
    #------------------------------------------
    EOF
    #
    cat /etc/bashrc 
    ```

Either log out and back in again, or issue this terminal command:

     . /etc/bashrc

TEST: did $FSLDIR get exported correctly? This should return "/usr/local/fsl" (no quotes) :

     echo $FSLDIR

TEST: Does fslview exist in /Applications?

    ls /Applications
    # ...if not create a shortcut like this:
    sudo ln -s /usr/local/fsl/bin/fslview.app /Applications/fslview.app

TEST: we should be able to open /Applications/fslview.app from the commandline:

     open /Applications/fslview.app

TEST: we should be able to open fslview.app from the commandline :

     fslview ${FSLDIR}/data/standard/MNI152_T1_2mm_LR-masked.nii.gz


Install and run FSL test suite ("FEEDS"):
---------------------------------------------------------------

Download [FEEDS from FSL (> 270 MB)](http://fsl.fmrib.ox.ac.uk/fsldownloads/), or neurodebian can download via command:

    sudo apt-get install fsl-feeds

Extract and [run FEEDS](http://fsl.fmrib.ox.ac.uk/fsl/feeds/doc/) :

    # (on neurodebian, this is all replaced by command fsl-selftest or /usr/bin/time fsl-selftest)
    # 2013 i7 imac:  971.77 real  951.78 user 22.83 sys
    # 2012 i7 rMBP: 1108.44 real 1080.64 user 31.06 sys
    cd ~/Downloads       # (or the folder where you saved your download)
    tar -zxvf fsl-*-feeds.tar.gz
    cd feeds
    /usr/bin/time ./RUN all


AFNI
==========

Installing AFNI on Mac OS X Mountain/Lion:
------------------------------------------------------------

Before installing FSL, freesurfer, or  AFNI on Mountain/Lion be sure to install [XQuartz](http://xquartz.macosforge.org), and logout and then back in. 

Then download latest AFNI for Mac, unzip, and move to a reasonable location: 

    cd ~/Downloads
    curl -O http://afni.nimh.nih.gov/pub/dist/tgz/macosx_10.7_Intel_64.tgz
    tar -zxvf macosx_10.7_Intel_64.tgz
    sudo mv macosx_10.7_Intel_64 /usr/local/abin

Add AFNI's new location to the path in /etc/bashrc :

    #    WARNING: note the \${escapedVariables} below, which
    #    are escaped for heredoc (http://goo.gl/j3HMJ). 
    #    Un-escape them if manually typing into a text editor.
    #    Otherwise, just paste these lines to your bash prompt
    #    (up to and including "EOF" line):
    #
    editDate=`/bin/date +%Y%m%d`
    editTime=$(date +%k%M)
    sudo tee -a /etc/bashrc >/dev/null <<EOF
    #------------------------------------------
    # on ${editDate} at ${editTime}, user $USER  
    # added some AFNI environmental variables:
    export PATH=/usr/local/abin:${PATH}
    export AFNI_ENFORCE_ASPECT=YES
    echo ""
    echo "----------- active afni version and variables: -----------"
    afni -ver
    echo -n "The command 'afni' will launch: "
    which afni
    echo -n "The command '3dinfo' will launch: "
    which 3dinfo
    echo "AFNI environmental variables, if any exist:"
    env | grep AFNI
    echo "----------------------------------------------------------"
    echo ""
    #------------------------------------------
    EOF
    #
    cat /etc/bashrc

Either log out and back in again, or issue this terminal command:

    . /etc/bashrc 

TEST: Open a new terminal window and test your afni install by issuing the command "afni" (no quotes) to open GUI. Confirm whether AFNI_ENFORCE_ASPECT is working effectively (see below)


ITK-SNAP
====================

Install the ITK-SNAP binary on OS X Mountain/Lion:
-----------------------------------------------------------------

Download latest version from the [ITK-SNAP Downloads page](http://www.itksnap.org/pmwiki/pmwiki.php?n=Main.Downloads).
* Choose "MacOS Binary (Intel, 64 bit, OSX 10.5+)"
* Drag the resulting ITK-SNAP.app to /Applications folder

MRIcron
==============================


Install MRIcron on Mac OS X Mountain/Lion:
--------------------------------------------------------------

1. Download latest [MRIcron binary](http://www.nitrc.org/projects/mricron) (probably called "MRIcron [month]/[year] osx.zip")

2. Unzip the downloaded file (which currently produces a folder called osx).

3. Install:

     ```
     cd ~/Downloads/osx/
     mv mricron.app dcm2niigui.app npm.app /Applications/
     ```

4. Move dcm2nii to a folder in the $PATH, e.g., /usr/local/bin:

    ```
    echo ${PATH}   # does this contain /usr/local/bin ?
    ls /usr/local  # is there a folder called bin inside of /usr/local ? If not: sudo mkdir /usr/local/bin
    sudo mv ~/Downloads/osx/dcm2nii /usr/local/bin/
    ```

Install MRIcron on Neurodebian (Ubuntu or Debian)
-----------------------------------------------------------------

I'm currently happy with the version in the neurodebian repos:

    sudo apt-get install mricron mricron-data mricron-doc

Remember that preferences are stored here if you would like to edit them:

    ~/.dcm2niigui/dcm2niigui.ini
    ~/.dcm2nii/dcm2nii.ini


MRIcroGL
===============


Install MRIcroGL on Mac OS X Mountain/Lion:
-----------------------------------------------------------------

1. Download the [latest version](http://www.mccauslandcenter.sc.edu/mricrogl/).
NB: pay attention to downloaded filename: if you already had osx.zip in your Downloads folder from mricrON, this mricrogl download may get called "osx(1).zip" etc.

2. Unzip and install it: 

    ```
    cd ~/Downloads
    mkdir mricrogl
    cd mricrogl
    unzip ~/Downloads/osx.zip
    sudo mv mricrogl.app /Applications/
    mv *.nii.gz /Users/Shared/sampleBrainVolumes/mricrogl # or other parent of sample data
    ```

3. Save the pdf manual somewhere handy.



ImageJ / FIJI
==================

Install FIJI on Mac OS X Mountain/Lion:
-----------------------------------------------------------------

1. Download the [fiji for macosx dmg](http://fiji.sc/Downloads)

2. Install:

    ```
    cd ~/Downloads
    open fiji-macosx.dmg
    cp -R /Volumes/Fiji/Fiji.app /Applications/
    hdiutil unmount /Volumes/Fiji
    open /Applications/Fiji.app 
    ```
    
Install ImageJ plugins:
-----------------------------------------------------------------

The location of the system-wide destination for imagej plugins differs by platform:

```
# for Fiji on OS X mountain/lion:
imagejSystemWidePluginDir=/Applications/Fiji.app/plugins
# for ImageJ on neurodebian ubuntu 12.04:
imagejSystemWidePluginDir=/usr/share/imagej/plugins

```

I always install this basic set of neuroimaging plugins for imagej:

* Even in 2013, imagej/fiji can't open .nii.gz files without a plugin.
Download and install the [imagej nifti plugin](http://rsb.info.nih.gov/ij/plugins/nifti.html) :

  ```
  cd ~/Downloads
  curl -O http://rsb.info.nih.gov/ij/plugins/download/jars/nifti_io.jar
  sudo mv nifti_io.jar ${imagejSystemWidePluginDir}/
  ```

   * Re-open imagej/fiji. Opening nii.gz files should now work via drag & drop, or File->Import->Nifti/Analyze.
   * Also should now see five new commands in imagej/fiji:  
     * File/Import/NIfTI-Analyze, 
     * File/Save As/Analyze (NIfTI-1), 
     * File/Save As/NIfTI-1, 
     * File/Save As/Analyze 7.5 and 
     * Analyze/Tools/Coordinate Viewer

* The [Tudor DICOM](http://santec.tudor.lu/project/dicom) plugin provides a number of DICOM-related functions, including the ability to parse a DICOMDIR
file and inspect its images interactively. It can also act as a DICOM receiver.

   * Download the file [tudordicom_plugin_*.zip](http://www.santec.lu/project/dicom/download)
   * `cd ~/Downloads`
   * `unzip tudordicom_plugin_*.zip  # (...which creates a child directory called plugins)`
   * `sudo mv plugins/TudorDICOM* ${imagejSystemWidePluginDir}/`
   * `rmdir plugins`
   * restart imagej/fiji
   * The imagej/fiji should now have two new submenus: Plugins/TudorDICOM submenu and Plugins/TudorDICOMLibs
   * Some additional 3D components may need to be installed system-wide:
   		* Launch imagej/fiji as an administrative user (e.g., sudo imagej)
   		* Select menu item Plugins -> TudorDICOMLibs -> 3D Viewer
   		* If java 3D is not already installed, will display message "Java 3D seems not to be installed. Auto-install?"
   		* Allow installation. Shouldn't ask about destination.
   		* Restart imagej/fiji as a normal user and attempt to open 3D viewer again. Should not produce message about java 3D installation.
  

  

SPM (Mountain Lion)
=========================

1. Download the latest [SPM 8 package](http://www.fil.ion.ucl.ac.uk/spm/software/spm8/)

2. Unzip the downloaded SPM package and move the resulting SPM folder to /usr/local , 
   so that SPM program files are not in /usr/local/spm.

3. Open matlab and issue the command "userpath;" (no quotes). This will tell you what
   folder Matlab is going to look in for certain files. You care about this folder because 
   in the next step you are going to create a special file that gets placed there.

4. In the Matlab window issue the command "pathtool;" (no quotes). This opens a new window. 
   In this window, add /usr/local/spm to the list of folders that Matlab searches for files. 
   (NB: Upon clicking "save", this window may complain about problems creating a file called pathdef.m, and ask you where you would like to put this file. Put it on the folder that was returned by the userpath command you entered earlier.)

5. Test your installation by closing and then reopening Matlab. Type "spm;" (no quotes) at the Matlab prompt, and the SPM graphical user interface should open.

Acknowledgments
=========================
This guide borrows liberally from [stowler's setupNeuroimagingEnvironment.md](https://github.com/stowler/brainwhere/blob/master/docs/setupNeuroimagingEnvironment.md).  

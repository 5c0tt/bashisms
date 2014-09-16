Last Modified 09/08/14 — 08:06:59 PM • Scott Haneda • [@cometbus](https://twitter.com/cometbus)

# Bash Tips and Tricks

### Use expanded Print dialog by default ( Single User )

	defaults write -g PMPrintingExpandedStateForPrint -bool TRUE

### To set this preference for all users ( Multi-User )

	defaults write /Library/Preferences/.GlobalPreferences PMPrintingExpandedStateForPrint -bool TRUE


### Use expanded Save/Open dialog by default

	defaults write -g NSNavPanelExpandedStateForSaveMode -bool TRUE

### Mouse Scaling

	defaults write -g com.apple.trackpad.scaling 4.7
	defaults read -g com.apple.trackpad.scaling

### Key Repeat Rate

	defaults write NSGlobalDomain KeyRepeat -int 1

1. Log out and log in again.
2. The fastest setting obtainable via System Preferences is 2 (lower numbers are faster)
3. You may also want to try a value of 1 if 0 seems too fast.
4. You can always visit the Keyboard & Mouse System Preferences panel to undo your changes.

### Disable Dashboard Widgets ( 10.8 OK )

	defaults write com.apple.dashboard mcx-disabled -boolean YES
	killall Dock

### Remove the TopSites feature from Safari

	defaults write com.apple.Safari DebugSnapshotsUpdatePolicy -int 2

### Show all hidden files

	defaults write com.apple.finder AppleShowAllFiles FALSE; killall Finder

### Clean `ls` listing in bash shell

	ls -t
	* - searches for any pattern
	? - searches for one, and only one, character
	[] - searches for a single character, but allows a choice of characters

### Delete all .DS_Store files

	find . -name .DS_Store -exec rm -f {} \;


find . -name .DS_Store -exec rm -f {} \;


### Update Locate Database

	sudo /usr/libexec/locate.updatedb

### One-Line Bash Repeat Loop

	while true; do ls -lh ~/path_to_direcotry_of_files; sleep .2; done;
	- or -
	while true; do echo 'test'; sleep .2; done;

### General hints
* [Speed up your Mac with the Terminal](http://www.chriswrites.com/2012/02/5-ways-to-speed-up-your-mac-just-using-terminall/)
* [Customizations for Mountain Lion](http://www.philmug.ph/forum/f19/customizations-mountain-lion-%5Bterminal-commands%5D-85165/)
* [Best list of defaults write commands I have ever seen](http://mths.be/osx)
* [Setup your computer from scratch with a script](https://github.com/mathiasbynens/dotfiles/blob/master/.osx)

### Removes duplicate files from a direcory based on it's checksum
	cd /Volumes/vorbis/images
	pwd
	ls -la | wc -l
	rm /tmp/filelist
	find . -maxdepth 1 -type f | xargs -n 100 cksum > /tmp/filelist
	rm -v `cat /tmp/filelist | sort | awk 'BEGIN {c=0} {if ($1 == c) print $3; c=$1}'`
	rm /tmp/filelist
	ls -la | wc -l

### Bash repeat loop one-liner
	while true; do ls -lah | grep -i home; echo \n;    sleep 0.5; done

### Create `tar` archive

	# `l` is an alias for `ls -la`

	# Make a dir to test in on the ~/Desktop called "~/Desktop/working"
	# change `cd` into that dir
	@foonty Desktop $mkdir -p working; cd working

	# Show that we are where we think we are in the filesystem
	@foonty working $pwd
	/Users/you/Desktop/working

	# Make a bunch of files that we will test for "tar'ing" up
	@foonty working $touch file1 file2 file3 file4 file5 1 2 3 4 5

	# List the files we just made to make sure we made what we wanted
	@foonty working $l
	-rw-r--r--   1 you  staff    0 Aug 11 04:22 1
	-rw-r--r--   1 you  staff    0 Aug 11 04:22 2
	-rw-r--r--   1 you  staff    0 Aug 11 04:22 3
	-rw-r--r--   1 you  staff    0 Aug 11 04:22 4
	-rw-r--r--   1 you  staff    0 Aug 11 04:22 5
	-rw-r--r--   1 you  staff    0 Aug 11 04:22 file1
	-rw-r--r--   1 you  staff    0 Aug 11 04:22 file2
	-rw-r--r--   1 you  staff    0 Aug 11 04:22 file3
	-rw-r--r--   1 you  staff    0 Aug 11 04:22 file4
	-rw-r--r--   1 you  staff    0 Aug 11 04:22 file5

	# tar the files up into collection.tar
	# note that the order of arguments is [TapeArchive].tar [[List of files]]
	# File globbing is supported, so file* grabs file1, file2, file3, file4, files5 etc.
	@foonty working $tar cvf collection.tar 1 2 3 4 5 file*
	a 1
	a 2
	a 3
	a 4
	a 5
	a file1
	a file2
	a file3
	a file4
	a "file5"
	
	       ================= Alternate Syntax =================
          ( Do it all in one shot, a tar and a gzip in one go )
          Came from [tecmint.com](http://www.tecmint.com/18-tar-command-examples-in-linux/)
                
    Alternate syntax — ( uses file extension .tar.gz )
    @foonty tar cvzf MyImages-14-09-12.tar.gz /home/MyImages
     - OR - ( uses file extension .tgz )
    @foonty tar cvzf MyImages-14-09-12.tgz /home/MyImages

    /home/MyImages/
    /home/MyImages/Sara-Khan-and-model-Priyanka-Shah.jpg
    /home/MyImages/RobertKristenviolent101201.jpg
    /home/MyImages/Justintimerlakenaked101125.jpg
    /home/MyImages/Mileynudephoto101203.jpg
    /home/MyImages/JenniferRobert101130.jpg
    /home/MyImages/katrinabarbiedoll231110.jpg
    /home/MyImages/the-japanese-wife-press-conference.jpg
    /home/MyImages/ReesewitherspoonCIA101202.jpg
    /home/MyImages/yanaguptabaresf231110.jpg

	# List the files to see that they have been tar'd up into collection.tar
	@foonty working $l | grep -i collection
	-rw-r--r--   1 you  staff  6144 Aug 11 04:23 collection.tar

	# Clean-up after myself, leaving just the tar archive
	@foonty working $rm 1 2 3 4 5 file*

	# Just collection.tar remains now
	@foonty working $l
	-rw-r--r--  1 you  staff  6144 Aug 11 04:23 collection.tar

	# Delete the "working" dir from ~/Desktop/working
	@foonty Desktop $rmdir ~/Desktop/working

	# At this point, the "working" directory was gone.  I had previously `tar xvf collection.tar`
	# on the files to make sure that the files could be restored without trouble.  I'm still not sure
	# about meta data, but the space and aggravation that Caroline and Jake are causing my
	# computer—there isn't much more I am willing to put into this project.


### Disable/Enable Spotlight from the Command Line
Taken from [http://www.tekrevue.com/](http://www.tekrevue.com/tip/three-ways-to-prevent-spotlight-from-indexing-items-on-your-mac/)

	sudo launchctl unload -w /System/Library/LaunchDaemons/com.apple.metadata.mds.plist

	sudo launchctl load -w /System/Library/LaunchDaemons/com.apple.metadata.mds.plist

	Also, add your boot drive to the System Preferences exclusions temporarily.

#### One line repeat loop with multiple commands#
	while true; do ls -lah | grep -i home; echo \n;    sleep 30; done

#### Another example of a long form repeat loop#

	#!/bin/sh
	cd ~/Documents/place/I/want/to//watch

	while true;
	    do
			# Whatever other commands you desire,
			# Usually one per line for readability
			ls -l | grep caroline\.dmg;
			ls -lh | grep caroline\.dmg;
			echo
			sleep 5
	done

* [PDF—LightPaper title problems example](http://dl.dropbox.com/u/340087/drops/08.11.14/bash-tips-+tricks-07-084726.pdf)
* [HTML—LightPaper title problems example](http://dl.dropbox.com/u/340087/drops/08.11.14/bash-tips-+tricks-c9-084941.html)

## File System Commands
The below commands will in general show you what is going on in real-time with your disk access.

Some of the below are not default included in Mac OS X and may need to be installed:

* `fs_usage`
* `lsof`
* `iostat`
* `iotop`
* `sar`
* `dstat`
* `vmstat`
* `iozone`
* `spindump`

You can find out more about these types of commands at [this StackOverflow question](http://stackoverflow.com/questions/25477875/what-is-the-name-of-the-command-that-shows-disk-access-in-real-time).

##Change the default save location from iCloud to On My Mac 
The default save location for [TextEdit](file:///Applications/TextEdit.app) ( *and other apps that can store documents on iCloud* ) is iCloud. It takes a few clicks if you want to expand the save dialog and save a file locally. There is no way to change this in the GUI, but if you run the following command in Terminal, this will set the default save location to **On My Mac** for all iCloud-compatible apps:

    defaults write NSGlobalDomain NSDocumentSaveNewDocumentsToCloud -bool false
 
To change this back to the default, run this command:

    defaults write NSGlobalDomain NSDocumentSaveNewDocumentsToCloud -bool true
    
Because *DashBoard* runs as a background app through the *Dock*, you will need to restart the *Dock* to see the changes to effect.  Either restart your computer, or run this common in your Terminal:

    killall -1 Dock

## `spindump` Debugging Crashed Applications
[`spindump`](https://developer.apple.com/library/mac/documentation/Darwin/Reference/Manpages/man8/spindump.8.html) is an application that when another application is stuck and nee qui `apindump` will eventually be called.  It usually is called automatically, according to the man page, a sample of the offending/stuck app are stored at:

    /Library/Logs/DiagnosticReports/
    
I don't know how unusual it is, but I have/had 49 total items in 

    `/Library/Logs/DiagnosticReports/`
    
which to me seems like a lot. That is a lot of application crashes, however, I was messing with that "*file corruption*" or the drive in haircutting boi's! computer.

When an application hangs, `spindump` is called.  This is supposed to release the crashed app, though in my experience, it never does.  It does however, write out a sample of the process in the above `DiagnosticReports` location.

### Manually calling `spindump`
You can also, ( *as root* ) call `spindump` when you know things are going wrong.  The Terminal will report:

    $spindump
    Sampling all processes for 10 seconds with 10 milliseconds of run time between samples
    Sampling completed, processing symbols...
    Spindump analysis written to file /tmp/spindump.txt
    
Each time you call the `spindump` command, the filename is incremented in the `/tmp` directory.  [Here is a sample](resources/spindump/spindump.txt) of the file that you can look at to see how to formatting is and what information is provided.

## Mac OS X Dictation
You can now talk to your Mac, there are two ways this can happen:

1. Talk — data gets sent to Apple, decoded, and sent back
2. Talk — a large, 750MB local file is used to do the dictation, keeping Apple away from your private conversations.

The file that Apple Downloads is controlled in the System Preferences of the Dictation setting. It is large, and can be found at: 

    /System/Library/Speech/Recognizers/SpeechRecognitionCoreLanguages
    
Feel free to safely delete the file if you turn off dictation, which is very CPU heavy on older machines.





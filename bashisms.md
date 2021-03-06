Last Modified 09/21/14 - 11:03:18 P.M.  
Scott Haneda [@scotthaneda](https://twitter.com/scotthaneda)

# Bash Tips and Tricks

## Use expanded Print dialog by default ( Single User )

	defaults write -g PMPrintingExpandedStateForPrint -bool TRUE

## To set this preference for all users ( Multi-User )

	defaults write /Library/Preferences/.GlobalPreferences PMPrintingExpandedStateForPrint -bool TRUE


## Use expanded Save/
Open dialog by default

	defaults write -g NSNavPanelExpandedStateForSaveMode -bool TRUE

## Mouse Scaling

	defaults write -g com.apple.trackpad.scaling 4.7
	defaults read -g com.apple.trackpad.scaling

## Key Repeat Rate

	defaults write NSGlobalDomain KeyRepeat -int 1

1. Log out and log in again.
2. The fastest setting obtainable via System Preferences is 2 (lower numbers are faster)
3. You may also want to try a value of 1 if 0 seems too fast.
4. You can always visit the Keyboard & Mouse System Preferences panel to undo your changes.

## Yosemit Dock Dark Mode

Here is the script that does it… which I wrap up in an AppleScript and then put into my startup items as it needs to run once per reboot or the settings are lost.

	#!/bin/bash
	/usr/bin/defaults write NSGlobalDomain AppleInterfaceStyle Dark; sleep 1;
	/usr/bin/killall Dock; sleep 1;
	/usr/bin/defaults remove NSGlobalDomain AppleInterfaceStyle
	
Stuff this in an AppleScript file:

	run_script.scpt
	do shell script "cd ~/Applications/custom_dark_mode; pwd; ./dark_mode.sh"

	# Take the above `run_script.sh` and put it into an AppleScript Application, save 
	# as run_script.app:
	

## Disable Dashboard Widgets ( 10.8 OK )

	defaults write com.apple.dashboard mcx-disabled -boolean YES
	killall Dock

## Remove the TopSites feature from Safari

	defaults write com.apple.Safari DebugSnapshotsUpdatePolicy -int 2

## Show all hidden files

	defaults write com.apple.finder AppleShowAllFiles FALSE; killall Finder

## Clean `ls` listing in bash shell

	ls -t
	* - searches for any pattern
	? - searches for one, and only one, character
	[] - searches for a single character, but allows a choice of characters

## Delete all .DS_Store files

	find . -name .DS_Store -exec rm -f {} \;

## Find all of a file, and copy that result set elsewhere
Take note, with cp, you may want to be careful and add in logic to make sure that the filename is not common, of you will write over the file multiple times.

    find . -type f -exec cp {} ../done \;

This says find all files in the current working directory, of the kind of type of file, and copy them to a directory called done that is one level up at ../done from the . location.

find . -name .DS_Store -exec rm -f {} \;


## Update Locate Database

	sudo /usr/libexec/locate.updatedb

## One-Line Bash Repeat Loop

	while true; do ls -lh ~/path_to_direcotry_of_files; sleep .2; done;
	- or -
	while true; do echo 'test'; sleep .2; done;
	- or - 
	while :; do clear; your_command; sleep 2; done

## General hints
* [Speed up your Mac with the Terminal](http://www.chriswrites.com/2012/02/5-ways-to-speed-up-your-mac-just-using-terminall/)
* [Customizations for Mountain Lion](http://www.philmug.ph/forum/f19/customizations-mountain-lion-%5Bterminal-commands%5D-85165/)
* [Best list of defaults write commands I have ever seen](http://mths.be/osx)
* [Setup your computer from scratch with a script](https://github.com/mathiasbynens/dotfiles/blob/master/.osx)

## Removes duplicate files from a direcory based on it's checksum
	cd /Volumes/vorbis/images
	pwd
	ls -la | wc -l
	rm /tmp/filelist
	find . -maxdepth 1 -type f | xargs -n 100 cksum > /tmp/filelist
	rm -v `cat /tmp/filelist | sort | awk 'BEGIN {c=0} {if ($1 == c) print $3; c=$1}'`
	rm /tmp/filelist
	ls -la | wc -l

## Bash repeat loop one-liner
	while true; do ls -lah | grep -i home; echo \n;    sleep 0.5; done
	
## Bash check if directory is empty or contains items
	find /dir/name -type -f -exec command {} \;

###Alternate syntax for GNU/BSD find command syntax:

	find /path/to/dir -maxdepth 0 -empty -exec echo {} is empty. \;

###Check if /tmp has is empty or not
	
	find "/tmp" -type f -exec echo Found file {} \;	
	
###There are two alternate methods as well:

	$ [ "$(ls -A /path/to/directory)" ] && echo "Not Empty" || echo "Empty"
	$ [ "$(ls -A /tmp)" ] && echo "Not Empty" || echo "Empty"
	
##An example of the above directory check but in a script

	#!/bin/bash
	FILE=""
	DIR="/tmp"
	# init
	# look for empty dir 
	if [ "$(ls -A $DIR)" ]; then
	     echo "Take action $DIR is not Empty"
	else
	    echo "$DIR is Empty"
	fi
	# rest of the logic

## Create `tar` archive

	# Make a dir to test in on the ~/Desktop called "~/Desktop/working"
	# change `cd` into that dir
	@foonty Desktop $mkdir -p ~/Desktop/working; cd ~/Desktop/working

	# Show that we are where we think we are in the filesystem
	@foonty working $pwd
	/Users/you/Desktop/working

	# Make a bunch of files that we will test with for "tar'ing" up
	@foonty working $touch file1 file2 file3 file4 file5 1 2 3 4 5

	# List the files we just made to make sure we made what we wanted
	@foonty working $ls -la
	-rw-r--r--  you  staff  Aug 11 04:22 1
	-rw-r--r--  you  staff  Aug 11 04:22 2
	-rw-r--r--  you  staff  Aug 11 04:22 3
	-rw-r--r--  you  staff  Aug 11 04:22 4
	-rw-r--r--  you  staff  Aug 11 04:22 5
	-rw-r--r--  you  staff  Aug 11 04:22 file1
	-rw-r--r--  you  staff  Aug 11 04:22 file2
	-rw-r--r--  you  staff  Aug 11 04:22 file3
	-rw-r--r--  you  staff  Aug 11 04:22 file4
	-rw-r--r--  you  staff  Aug 11 04:22 file5

	# tar the files up into collection.tar
	# note that the order of arguments is [TapeArchive].tar [[List of files]]
	# File globbing is supported, so "file*" grabs "file1", "file2", "file3", "file4", "files5" etc.
	@foonty working $tar cvf collection.tar 1 2 3 4 5 file*
	* I have no idea what the leading "a" is all about in the tar output…
	a 1
	a 2
	a 3
	a 4
	a 5
	a file1
	a file2
	a file3
	a file4
	a file5
	
	Alternate Syntax
	( Do it all in one shot, a tar and a gzip in one go )
	Came from http://www.tecmint.com/18-tar-command-examples-in-linux/
                
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
	@foonty working $ls -la | grep -i collection
	-rw-r--r--  you  staff  6144 Aug 11 04:23 collection.tar

	# Clean-up after myself, leaving just the tar archive
	@foonty working $rm 1 2 3 4 5 file*

	# Only collection.tar remains now
	@foonty working $ls -la
	-rw-r--r--  1 you  staff  6144 Aug 11 04:23 collection.tar

	# Delete the "working" dir from ~/Desktop/working
	@foonty Desktop $rmdir ~/Desktop/working

	# At this point, the "working" directory is gone.  I had previously `tar xvf collection.tar`
	# on the files to make sure that the files could be restored without trouble.  I'm still not sure
	# about meta data, but the space and aggravation that Caroline and Jake are causing my
	# computer—there isn't much more I am willing to put into their project.
	
## Extract a tar archive to a specific location instead of moving it
	$ tar xvf -C tmp/a/b/c newarc.tar.gz

## All you could ever need to know about `curl`
* Taken from: [curl.haxx.se/docs/faq.html](http://curl.haxx.se/docs/faq.html)

## All you could ever want to know about the `alias` command
* [Bash alias tips and tricks](http://www.cyberciti.biz/tips/bash-aliases-mac-centos-linux-unix.html)

## Disable/Enable Spotlight from the Command Line
Taken from [http://www.tekrevue.com/](http://www.tekrevue.com/tip/three-ways-to-prevent-spotlight-from-indexing-items-on-your-mac/)

	sudo launchctl unload -w /System/Library/LaunchDaemons/com.apple.metadata.mds.plist

	sudo launchctl load -w /System/Library/LaunchDaemons/com.apple.metadata.mds.plist

	Also, add your boot drive to the System Preferences exclusions temporarily.

## One line repeat loop with multiple commands
	while true; do ls -lah | grep -i home; echo \n;    sleep 30; done
	while sleep 2; do echo thinking; done

## Another example of a long form repeat loop

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

## Tons of examples of shell loops
* [http://www.cyberciti.biz/faq/bash-loop-over-file/](http://www.cyberciti.biz/faq/bash-loop-over-file/)

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
The default save location for [*TextEdit*](file:///Applications/TextEdit.app) ( *and other apps that can store documents on iCloud* ) is *iCloud*. It takes a few clicks if you want to expand the save dialog and save a file locally. There is no way to change this in the GUI, but if you run the following command in the *Terminal*, this will set the default save location to ***On My Mac*** for all iCloud-compatible apps:

    defaults write NSGlobalDomain NSDocumentSaveNewDocumentsToCloud -bool false
 
To change this back to the default, run this command:

    defaults write NSGlobalDomain NSDocumentSaveNewDocumentsToCloud -bool true
    
Because *DashBoard* runs as a background app through the *Dock*, you will need to restart the *Dock* to see the changes to effect.  Either restart your computer, or run this common in your Terminal:

    killall -1 Dock

## `spindump` Debugging Crashed Applications
[`spindump`](https://developer.apple.com/library/mac/documentation/Darwin/Reference/Manpages/man8/spindump.8.html) is an application that when another application is stuck and needs quitting, `spindump` will eventually be called.  It usually is called automatically, according to the man page, a sample of the offending/stuck app are stored at:

    /Library/Logs/DiagnosticReports/
    
I don't know how unusual it is, but I have/had 49 total items in 

    `/Library/Logs/DiagnosticReports/`
    
which to me seems like a lot. That is a lot of application crashes, however, I was messing with "*file corruption*" on the drive from my friend Jake's computer.

When an application hangs, `spindump` is called.  This is supposed to release the crashed app, though in my experience, it never does.  It does however, write out a sample of the process in the above `DiagnosticReports` location.

## Manually calling `spindump`
You can also, ( *as root* ) call `spindump` when you know things are going wrong.  The Terminal will report:

    $spindump
    Sampling all processes for 10 seconds with 10 milliseconds of run time between samples
    Sampling completed, processing symbols...
    Spindump analysis written to file /tmp/spindump.txt
    
Each time you call the `spindump` command, the filename is incremented in the `/tmp` directory.  [Here is a sample](resources/spindump/spindump.txt) of the file that you can look at to see how to formatting is and what information is provided.

## A little trick when you have massive data scrolling in a Terminal
Often times, you will be running a more, tail -f, or some pipe of a command over a fast moving log file.  The text scrolls by super fast, and sliding the page slider to the bottom, by the time you release, you have already stopped several pages of data ahead of the last bits sent in.  My solution which I randomly figured out:
`option-down arrow` key.

## Mac OS X Dictation
You can now talk to your Mac, there are two ways this can happen:

1. Talk — data gets sent to Apple, decoded, and sent back
2. Talk — a large, 750MB local file is used to do the dictation, keeping Apple away from your private conversations.

The file that Apple Downloads is controlled in the System Preferences of the Dictation setting. It is large, and can be found at: 

    /System/Library/Speech/Recognizers/SpeechRecognitionCoreLanguages
    
Feel free to safely delete the file if you turn off dictation, which is very CPU heavy on older machines.

##hdiutil usage examples
* [http://commandlinemac.blogspot.com/2008/12/using-hdiutil.html](http://commandlinemac.blogspot.com/2008/12/using-hdiutil.html)

##Explanation of single, double, square, and curly brackets in bash
* [http://stackoverflow.com/questions/2188199…](http://stackoverflow.com/questions/2188199/how-to-use-double-or-single-bracket-parentheses-curly-braces)

## Find files by size, then possibly perform an action on them
Sometimes you need to find throughout a huge list of files containing directories with subdirectories, looking for all files greater than or smaller than a certain criteria.  You may then want to delete or move them elsewhere.

To start with, we need to locate them:

    find /path/to/files. -size +5M
   
This will find all files 5Mb or greater.  You can use k for kilobytes, and others, look at the manage for `man find` and you can see all the options.

I usually use a . as the "/path/to/files/" location for the current working directory.

If you then wanted to delete the files it has found, you can use `-exec` or `xargs`, from what I understand, -exec can fail on really large file listings, has issues with spaces in file-names, and other problems that I am yet to fully research.  For now, read this [link at StackOverFlow](http://stackoverflow.com/questions/10079740/find-files-with-size-in-unix).

The general syntax is:

    $find . -iname "*.mp3" -exec ... 
    $find . -iname "*.txt" -print | xargs file
    
The first is just a find of all mp3 files passing in the -exec where you can issue a command such as `mv`, `rm`, etc.

The second will work too, even better as apparently you need not worry about spaces in file names, strange characters, and very large result sets.  The example simply pipes the results to `xargs` and then issues the `file` command, which shows something like this:

    $find . -iname "*.txt" -print | xargs file
    ./resources/spindump/spindump.txt: ASCII news text, with very long lines







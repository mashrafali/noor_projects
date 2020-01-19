Describe Enterprise/IT/Docs/HowTo/Linux/SIP/SIP_Installation here.



{{{
Author       : Mahmoud Hamdy
Version      : 1.0
Creation date: 2015-02-08
}}}


<<TableOfContents(3)>>

=== Elastix Installation ===

• You will need to download the ISO from the Elastix website  http://www.elastix.org 

• Burn the ISO image that you have just downloaded to a blank CD.

• Ensure that your PC will boot from the CD. If necessary change the BIOS
settings to enabled this.

• **NOTE: This will erase all data on the hard drives of the PC.
If you have 2 drives, both may be blown away as well - beware.

• Boot your Elastix box with the CD in the CD Drive and press enter. After a few
seconds, the following screen will appear. You press F2 to see the various
options. However, it is not really necessary. Just press [Enter] to start the
installation

{{attachment:Capture1.PNG}}


• Press the Enter key to start the installation.

• After initial system detection, you will be asked to select the type of keyboard that
you will be using. Use your cursor keys to navigate the keyboard types and use
the Tab key to move the focus to the OK or Back option. Select the appropriate
keyboard. Selected us and move the focus to OK and hit [Enter]


{{attachment:Capture2.PNG}}


• After system hardware probing is completed, you will be asked to select the Time
Zone you are in.

{{attachment:Capture3.PNG}}

• Use your cursor arrow keys to navigate to the appropriate time zone. In our case,
we will selected Australia/NSW. Use the TAB key to move the focus to OK and
hit [Enter] to accept (see cheat sheet to set NTP time manually).

• Next you will be asked to enter your root password (remember this password).

{{attachment:Capture4.PNG}}


• After entering and confirming your password, installation will now commence by
first formatting the Hard Disk/s.

• From this point it will take about 30-45 minutes for the installation to be complete
and ready for the configuration stage.

• During this stage, you will see screens similar to the following. Linux and the
required files are being installed. All you do is, wait for it to finish.

{{attachment:Capture5.PNG}}

• After Linux is loaded the CD will eject. Take the CD out and wait for the system to
reboot.

• When it reboots, you will be presented with the Elastix splash screen.

• After a moment, it will continue and you will see lots of lines of codes. This
process will take a while because it is building Asterisk.

• When Asterisk build is complete, it will reboot itself. Make sure that you remove
the CD from the CD Drive before the PC reboots itself.

• Once rebooted, you will see the following screen where you can select the Elastix
distribution versions. In this case we shall leave it at the default.

{{attachment:Capture6.PNG}}

• After going through its initial startup script, Elastix is ready for you to configuring
and make changes to the system default.
Once Elastix has been installed, you may log in to Elastix if you need to do any command
line tasks.
Log in to your new Elastix (user: root, password: The one you gave earlier)

{{attachment:Capture7.PNG}}

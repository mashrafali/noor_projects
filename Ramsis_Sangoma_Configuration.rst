The following steps are needed in order to operate Ramsis PRI on the Sangoma card.
 
The red-coloured text is what I type followed by “Enter” for the next data.
 
[root@elastix-2 ~]#  wancfg_dahdi
 
########################################################################
#                       Sangoma Wanpipe #
#        Dahdi/Zaptel/SMG/TDMAPI/BOOT Configuration Script             #
#                             v2.39 #
#                     Sangoma Technologies Inc.                        #
#                        Copyright(c) 2009.                            #
########################################################################
 
Would you like to generate /etc/asterisk/chan_dahdi.conf
  1. YES
  2. NO
[1-2]: 1
 
############################################################################################################################################
 
Select media type for AFT-A104 on port 1 [slot:4 bus:2 span:1]
  1. T1
  2. E1
  3. Unused
  4. Exit
[1-4]: 2
 
############################################################################################################################################
 
Configuring port 1 on 104 as E1, line coding:HDB3, framing:CRC4
  1. YES - Keep these settings
  2. NO  - Configure line coding and framing
[1-2, ENTER='YES']: 2
 
############################################################################################################################################
 
Select line coding for port 1 on 104
  1. HDB3
  2. AMI
[1-2, ENTER='HDB3']: 1
 
############################################################################################################################################
 
Select framing for port 1 on 104
  1. CRC4
  2. NCRC4
  3. UNFRAMED
[1-3, ENTER='CRC4']: 2
 
############################################################################################################################################
 
Select clock for AFT-A104 on port 1 [slot:4 bus:2 span:1]
  1. NORMAL
  2. MASTER
[1-2]: 1
 
############################################################################################################################################
 
Select signalling type for AFT-A104 on port 1 [slot:4 bus:2 span:1]
  1. Zaptel/Dahdi - PRI CPE
  2. Zaptel/Dahdi - PRI NET
  3. Zaptel/Dahdi - E & M
  4. Zaptel/Dahdi - E & M Wink
  5. Zaptel/Dahdi - FXS - Loop Start
  6. Zaptel/Dahdi - FXS - Ground Start
  7. Zaptel/Dahdi - FXS - Kewl Start
  8. Zaptel/Dahdi - FX0 - Loop Start
  9. Zaptel/Dahdi - FX0 - Ground Start
  10. Zaptel/Dahdi - FX0 - Kewl Start
[1-10]: 1
 
###########################################################################################################################################
 
Select switchtype for AFT-A104 on port 1
  1. National ISDN 2
  2. Nortel DMS100
  3. AT&T 4ESS
  4. Lucent 5ESS
  5. EuroISDN
  6. Old National ISDN 1
  7. Q.SIG
[1-7]: 4
 
############################################################################################################################################
 
Would you like to enable hardware DTMF detection?
  1. YES
  2. NO
[1-2, ENTER='YES']: 1
 
############################################################################################################################################
 
Would you like to enable hardware fax detection?
  1. YES
  2. NO
[1-2, ENTER='NO']: 1
 
############################################################################################################################################
 
Configuring port 1 on AFT-A104 as a full E1
  1. YES - Use all channels
  2. NO  - Configure for fractional
[1-2, ENTER='YES']: 1
 
############################################################################################################################################
 
Select dialplan context for AFT-AA104 on port 1
  1. PSTN
  2. INTERNAL
[1-2]: 1
 
Port 1 on AFT-A104 configuration complete...
Press any key to continue:
 
T1/E1 card configuration complete.
Press any key to continue:
 
############################################################################################################################################
 
The above steps will be repeated three more times for ports 2, 3 & 4. After configuring port 4, continue with the following:
 
############################################################################################################################################
 
Port 4 on AFT-A104 configuration complete...
Press any key to continue:
 
T1/E1 card configuration complete.
Press any key to continue:
 
------------------------------------
Configuring ISDN BRI cards [A500/B700]
------------------------------------
 
No Sangoma ISDN BRI cards detected
 
Press any key to continue:
 
############################################################################################################################################
 
 
###################################################################
#                             SUMMARY                             #
###################################################################
 
   4 T1/E1 port(s) detected, 4 configured
   0 ISDN BRI port(s) detected, 0 configured
   0 analog card(s) detected, 0 configured
   0 usb device(s) detected, 0 configured
 
Configurator will create the following files:
     1. Wanpipe config files in /etc/wanpipe
     2. Dahdi config file /etc/dahdi/system.conf
     3. Chan-Dahdi config file /etc/asterisk/chan_dahdi.conf
 
 
Your original configuration files will be saved to:
     1. /etc/dahdi/system.conf.bak
     2. /etc/asterisk/chan_dahdi.conf.bak
 
 
Your configuration has been saved in /etc/wanpipe/debug-2014-05-19.tgz.
When requesting support, email this file to techdesk@sangoma.com
 
 
#############################################################################################################################################
 
 
Configuration Complete! Please select following:
  1. YES - Continue
  2. NO - Exit
[1-2]: 1
 
#############################################################################################################################################
 
Dahdi and Wanpipe configuration complete: choose action
  1. Save cfg: Restart Asterisk & Wanpipe now
  2. Save cfg: Restart Asterisk & Wanpipe when convenient
  3. Save cfg: Stop Asterisk & Wanpipe now
  4. Save cfg: Stop Asterisk & Wanpipe when convenient
  5. Save cfg: Save cfg only (Not Recommanded!!!)
  6. Do not save cfg: Exit
[1-6]: 1
 
Would you like to continue?
  1. No - exit
  2. YES
[1-2, ENTER='No']: 2
 
#############################################################################################################################################
 
Would you like wanrouter to start on system boot?
  1. YES
  2. NO
[1-2]: 1
 
Verifying Dahdi boot scripts...
Verifying Dahdi boot scripts...Enabled (level:26)
Verifying Dahdi shutdown scripts...Enabled (level:92)
Enabling wanrouter boot scripts ...(level:25)
Enabling wanrouter shutdown scripts ...(level:91)
 
Would you like to execute 'dahdi_cfg' each time wanrouter starts?
  1. YES
  2. NO
[1-2]: 1
 
Wait a minute then open “Hardware Detector” from Elastix GUI, you will find the PRI port is in GREEN

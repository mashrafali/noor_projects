## page was renamed from Enterprise/IT/TechDocs/HowTo/Linux/Joindomain
## page was renamed from Enterprise/IT/Docs/HowTo/Linux/Joindomain
## page was renamed from Enterprise/IT/Docs/How_To/Linux/Joindomain
{{{
Author       : Karim Farrag
Version      : 1.0
Creation date: 2012-07-11
Latest update: 2012-07-11
}}}

1-  open terminal <<BR>>	
2-  execute sudo apt-get install likewise-open5 <<BR>>
2-  Then execute
{{{
sudo domainjoin-cli join noor.net  username
}}}
you will be asked for the password of the user name <<BR>>
3-  Edit /etc/samba/lwiauthd.conf and add this line to it
{{{
winbind use default domain = yes
}}}
4-  Then from terminal 
{{{
sudo echo "greeter-show-manual-login=true" >> /etc/lightdm/lightdm.conf
}}}
5-  To add the user to the sudoer so he can act as administrator to this workstation create a Group on domain for all the users you want to make them sudoer on their computer name it sudoers<<BR>>
6-  Go to the file /etc/sudoersand and add the following line <<BR>>
%noor\\sudoers ALL=(ALL) ALL

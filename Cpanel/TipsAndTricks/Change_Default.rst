{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2014-11-19
Latest update: 2014-11-19
}}}
<<TableOfContents(3)>>

= How to change your default Index page in htaccess =
 			 																 				 					Written by [[https://plus.google.com/u/0/103891980085974966828/?rel=author|Scott Mitchell]] 							Views: 25,093 							Published: Oct 3, 2014 							Comments: [[http://www.inmotionhosting.com/support/website/htaccess/htaccess-change-index-page#comments|14]] 		 	 		  	  								The server looks for specifically named files as the first  page of your website, also known as the index page. The default order of  index file names our particular servers look through is ''index.htm'', ''index.html'', ''index.php'', and finally ''default.htm''. You can change the name of the index file your account looks for by altering the '''.htaccess'''  file. Perhaps you want to have a specific custom name for your index  file or maybe you are migrating from another host and the index page is  named differently. This way your internal links will not be broken by  renaming the index file.


== What is an htacces file? ==
 The .''htaccess'' file is a hidden text file within your hosting  account that can be very powerful. It is designed so that you can  modify it to change the behavior of your website that normally would  take higher access beyond your account. It acts as a liaison between  your domain and the server and can perform many functions.
   [[http://www.inmotionhosting.com/support/website/htaccess/what-is-the-htaccess-file-and-what-do-i-use-it-for|Learn more about htaccess]]


== Changing your default index file via htaccess ==
  1. First, you will need to [[http://www.inmotionhosting.com/support/edu/cpanel/getting-started-cpanel/how-to-log-into-cpanel|log into your cPanel]] to access the htaccess file.
 1. Find the '''Files '''category and click on the '''File Manager''' icon. <<BR>>[[http://cdn.inmotionhosting.com/support/images/stories/edu/htaccess/cpanel.png|{{http://cdn.inmotionhosting.com/support/images/stories/edu/htaccess/cpanel.png|cpanel}}]]
 1. A popup box will appear. For the primary domain, click on the '''Web Root''' radio button. For addon domains, click on the dropdown and find your desired addon domain name. Be sure the checkbox next to '''Show Hidden Files''' is checked. Click the '''Go''' button to enter the ''File Manager''. <<BR>>[[http://cdn.inmotionhosting.com/support/images/stories/edu/htaccess/file-manager-show-hidden.png|{{http://cdn.inmotionhosting.com/support/images/stories/edu/htaccess/file-manager-show-hidden.png|file-manager-show-hidden}}]]
 1. You should now be in the root folder of the domain you chose. Look for the ''.htaccess'' file and right click on it. This brings up a menu. Find and click on the '''Edit '''option. If you get a popup box, simply find and click the '''Edit '''button in the lower right corner to continue to the editor. <<BR>>[[http://cdn.inmotionhosting.com/support/images/stories/edu/htaccess/file-manager-htaccess.png|{{http://cdn.inmotionhosting.com/support/images/stories/edu/htaccess/file-manager-htaccess.png|file-manager-htaccess}}]]
 1. You are now in the htaccess editor. Paste the following code at the  top of the page to configure your desired index page. In our example  below, we decided to make the index page of our folders named  first.html.<<BR>> #Alternate default index page
 DirectoryIndex first.html You can also list more than one file in the configuration. The file will  be read left to right and check for them in that order. In this  example, we add index.htm, index.html, and index.php to the list. First  the server will check for first.html, if it does not find a file with  that name, it continues to index.htm and so on. #Alternate default index pages
 DirectoryIndex first.html index.htm index.html index.php
 1. Be sure to hit the '''Save Changes''' button in the upper right corner to save your new htaccess configuration.

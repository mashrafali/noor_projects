{{{
Author       : Ayman Tohamy
Title        : How to Rewrite cPanel Website without changing URL
Version      : 1.1
Creation date: 2016-02-09
Latest update: 2016-02-09
}}}
<<TableOfContents(4)>>

= How to redirect a domain without changing the URL =
In this article, we will give you 2 ways in how you can go  about redirecting your site to another URL, without changing the domain.

== Example 1: Redirect and keep everything after the URL ==
  The first option will show all of the same content on one URL as you  would another.  For example, if you just changed your domain to  DomainB.com, but you still have plenty of visitors coming to  DomainA.com, you would use this to show them all of the existing content  that is located on the new domain, without the need to update both  websites.

  To do this, you would modify your .htaccess file for the domain that your users will go to, and insert these lines of code:

 .{{{
 RewriteCond %{HTTP_HOST} ^DomainA.com
 RewriteRule ^(.*) http://DomainB.com/$1 [P]
 }}}
  If you are using the file-manager-in-cpanel be sure that you have the option to show hidden files selected.

  '''What does the above redirect do?'''
   After adding this line into your .htaccess file, you will be able to  go to DomainA.com/YourPage and it will show the content from  DomainB.com/YourPage


== Example 2: Redirect a domain to a specific url ==
  There is another way you can do your redirect to show a specific URL,  but keep the domain the same as well.  If you want visitors to go to  DomainA.com with a specific page in mind when doing so, you may use this  code:

 .{{{
  RewriteCond %{HTTP_HOST} ^DomainA.com
 RewriteRule ^(.*) http://DomainB.com/PathToPageHere [P]
 }}}

  You would use this method if, for example, you had an external blog  such as one on blogspot.com or maybe a shopping cart on etsy.com that  you want people to visit your domain without fully hosting the domain  there.  Now, visitors can access your site using your domain, but see  the content of an external URL.


== Example 3 Re-directing an IP address ==
  Occasionally, there will be a request to re-direct an IP address to a  specific URL. The following code shows how this can be done in the  .htaccess file.

 .{{{
  # Redirect all IP address (replace the ## with the IP address numerals) to same http://domain_name.com
 RewriteCond %{HTTP_HOST} ^##\.##\.##\.##
 RewriteRule (.*) http://domain_name.com/$1 [R=301,L]
 }}}

== Stipulations / Please read ==
  Warning: If using the first option, search engines such as Google or  Bing will see multiple sites with the same content and can cause ranking  drops in one, or both of the sites.  From an SEO standpoint, your best  option is to create 301 redirects instead.

  If you opt for the second option, and are using absolute links on  your site (meaning your links go to the full URL and not just an  individual file) any links will go to the original site that is  associated with that link.

#pragma section-numbers 2
{{{
Author       : Amr Mohsen
Version      : 1
Creation date: 2013-4-16
Latest update: 2014-7-24
}}}
<<TableOfContents(4)>>

== How to connect to First Login ==
{{{
ssh root@41.187.100.126 -p 60022
su firstlogin
vi /home/firstlogin/bin/firstlogin
}}}
{{{#!/usr/bin/python the script contain: # -*- coding: UTF-8 -*- import pymssql import optparse import datetime,time, sys import commands import MySQLdb import os class OptionParser(optparse.OptionParser):

 . def check_required (self, opt):
  . option = self.get_option(opt) # Assumes the option's 'default' is set to None! if getattr(self.values, option.dest) is None:
   . self.error("%s option not supplied" % option)

def getneedfuluser():

 . mypassword="mypassword" con = pymssql.connect(host='172.17.13.15',user='msamir',password='mypassword',database='Noor_ADSL_Tool2') cur = con.cursor() #system imports
 query="SELECT dpa.Username,c.ID ,CONVERT(char(10),as1.Status_Date,121) FROM Customers c INNER JOIN ADSL2_CustomerPort acp1 ON acp1.CustID" #query="SELECT dpa.Username,c.ID ,CONVERT(char(10),as1.Status_Date,121) FROM Customers c INNER JOIN ADSL2_CustomerPort acp1 ON acp1.CustI" cur.execute(query) while 1:
  . for y in cur.fetchall():
   . needfuluser={} needfuluser['name']=str(y[0]) needfuluser['id']=int(y[1]) needfuluser['status_date']=y[2] needfuluserlist.append(needfuluser)
  if 0 == cur.nextset():
   . break
 con.close() log=" Connecting to MS SQL and Find "+str(len(needfuluserlist))+" User NEED First Login Date" f.write(log+"\n")

def getfirstlogin():

 . radiuspasswd = "radpass0wrd" conn = MySQLdb.connect (host = "172.17.14.18",user = "firstlogin",passwd = "SqbDKmSlaENR", db = "radiusbe") cursor = conn.cursor () for needfuluser in needfuluserlist:
  . cursor.execute ("SELECT acctstarttime FROM radacct WHERE username='"+needfuluser['name']+"' AND acctstarttime >= '"+str(needfuluser['st) print ("SELECT acctstarttime FROM radacct WHERE username='"+needfuluser['name']+"' AND acctstarttime >= '"+str(needfuluser['status_date) while (1):
   . row = cursor.fetchone () if row == None:
    . break
   needfuluser['FIRSTLOGIN']=row[0] doneuserlist.append(needfuluser)
 cursor.close () conn.close () log=" Connecting to Radius SQL and Find "+str(len(doneuserlist))+" User have First Login Date" f.write(log+"\n") print "Find "+str(len(doneuserlist))+" User have First Login Date"

def getusergroup():

 . radiuspasswd = "radpass0wrd" conn = MySQLdb.connect (host = "172.17.14.18",user = "firstlogin",passwd = "SqbDKmSlaENR", db = "radiusbe") cursor = conn.cursor () for user in doneuserlist:
  . cursor.execute ("SELECT groupname FROM radusergroup WHERE username='"+user['name']+"' LIMIT 1 ") while (1):
   . row = cursor.fetchone () if row == None:
    . break
   user['GROUP']=row[0]
 cursor.close () conn.close ()

def updateuserinfo():

 . radiuspasswd = "radpass0wrd" conn = MySQLdb.connect (host = "172.17.14.18",user = "firstlogin",passwd = "SqbDKmSlaENR", db = "radiusbe") cursor = conn.cursor () for user in doneuserlist:
  . cursor.execute ("UPDATE userinfo SET name = '"+user['GROUP']+"', department = '"+user['cycle']+"' WHERE username = '"+user['name']+"' L)
 cursor.close () conn.close ()

def getusercycle ():

 . for user in doneuserlist:
  . if time.localtime()[2] >= 15:
   . user['cycle']="B1"
  else:
   . user['cycle']="B15"

def updateuserfirstlogin():

 . mypassword="mypassword" con = pymssql.connect(host='172.17.13.15',user='msamir',password='mypassword',database='Noor_ADSL_Tool2') cur = con.cursor() #system imports for user in doneuserlist:
  . query="Update Customers Set First_Login_Date=convert(varchar(50),'"+str(user['FIRSTLOGIN'])+"',120) where Id='"+str(user['id'])+"' an" log=" Trying to UPDATE "+str(user['name'])+" which have ID "+str(user['id']) f.write(log+"\n") log=" "+query f.write(log+"\n") cur.execute(query) con.commit()
 con.close() log=" "+str(len(doneuserlist))+" User UPDATE" f.write(log+"\n") print str(len(doneuserlist))+" User have been update"

def logfirstlogin():

 . mypassword="mypassword" con = pymssql.connect(host='172.17.13.15',user='msamir',password='mypassword',database='Noor_ADSL_Tool2') cur = con.cursor() #system imports for user in doneuserlist:
  . query="INSERT INTo FLD_Recrods (customer_Id,date_added) VALUES ("+str(user['id'])+",getdate())" cur.execute(query) con.commit()
 con.close()

def sendlogmail():

 . for user in doneuserlist:
  . fmail.write(str(user['name']).ljust(40)+str(user['FIRSTLOGIN']).ljust(50)+str(user['GROUP']).ljust(10)+str(user['cycle']).ljust(10)
 fmail.write("\n\n\nIf you find any none justified starting date feel free to contact responsible team\n") fmail.write("Thanks\nADSL Radar\n") fmail.close()
 cmd="cat /home/firstlogin/logs/MailBody | mail -s '"+str(len(doneuserlist))+" ADSL User Join NOOR **ADSL2-CLUB**' adslbusiness@noor.ne " os.system(cmd)

if __name__ == '__main__':

 . parser = OptionParser() parser.add_option("-f", "--file", dest="filename",help="write report to FILE", metavar="FILE") (options, args) = parser.parse_args() needfuluserlist=[] doneuserlist=[] f=open("/home/firstlogin/logs/log.txt", 'a') fmail=open("/home/firstlogin/logs/MailBody", 'w') fmail.write("Dear all,\nOur ADSL TOOL just received first log-in for the following customers\nPleas be advice that ADSL TOOL start cou) getneedfuluser() if len(needfuluserlist) == 0:
  . print "Did not match any Results"
 else:
  . print "Match Many Results "+str(len(needfuluserlist)) getfirstlogin()
  if len(doneuserlist) >= 1:
   . logfirstlogin() updateuserfirstlogin() getusercycle() getusergroup() updateuserinfo() sendlogmail()
  else:
   . fmail.close()
  f.close()

}}}

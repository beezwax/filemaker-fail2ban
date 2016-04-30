## filemaker-fail2ban
#### Fail2ban rules for FileMaker Server

These files files can be added to a fail2ban installment so that the relevant FileMaker Server logs will be included.

To install the fail2ban core files, you can use the source at fail2ban.org, or a Mac OS package manager like MacPorts or Homebrew.

#### Installation

For MacPorts, the install looks like this:

`sudo port install fail2ban`

To install from  source, the process is along these lines:

* open the Terminal
* fetch source (currently 0.9.3): `curl -O https://codeload.github.com/fail2ban/fail2ban/zip/master`
* unzip the archive: `tar xf master`
* `cd fail2ban-master`
* run the installer script: `sudo python setup.py install`

#### Configuration

To enable the filter, you'll need to add something like the following into your jail.local file (which you may need to create):

```
[filemaker-client]

enabled  = true
action   = pf[name=filemake-client, port=5003,16001,http,https]
dest     = youremail@domain.com
filter   = filemaker-client
logpath  = /Library/FileMaker Server/Logs/Event.log
maxretry = 6
```

If using the Ubuntu style macros this could be written more simply as:

```
[filemaker-client]

enabled  = true
port     = 5003,16001,http,https
filter   = filemaker-client
logpath  = /Library/FileMaker Server/Logs/Event.log
maxretry = 6
```

The following must be added to your /etc/pf.conf file to enable the fail2ban's ability to block addresses:
```
table <fail2ban> persist
block drop log quick from <fail2ban> to any
```

Reload the settings for PF:

`sudo pfctl -f /etc/pf.conf`

If not already enabled (likely), you'll need to enable PF to start filtering connections:

`pfctl -e`

#### Yosemite Configuration

If using 10.10, we can make an additional improvement. This however requires making a change to how FileMaker Server writes out the web server logs. Follow the directions at https://blog.beezwax.net/2015/06/30/keep-filemaker-server-web-logs-at-fixed-paths/ for details.

Once done, add the following to your jail.local file:

```
# Giving multiple log files in logpath would generate errors. Because there is
# a space in the path? So instead, we are creating separate entries for each log file.

[apache-badbots-1]

enabled  = true
filter   = apache-badbots
logpath  = /Library/FileMaker Server/HTTPServer/logs/access_log
action   = pf[name=apache-badbots, port="http,https"]
           sendmail-whois[name=%(hostname)s, dest=%(destemail)s, sender=%(hostname)s]
maxretry = 2

[apache-badbots-2]

enabled  = true
filter   = apache-badbots
logpath  = /Library/FileMaker Server/HTTPServer/logs/ssl_access_log
action   = pf[name=apache-badbots, port="http,https"]
           sendmail-whois[name=%(hostname)s, dest=%(destemail)s, sender=%(hostname)s]
maxretry = 2

[apache-badbots-3]

enabled  = true
filter   = apache-badbots
logpath  = /Library/FileMaker Server/HTTPServer/logs/fmsadminserver_access_log
action   = pf[name=apache-badbots, port="http,https"]
           sendmail-whois[name=%(hostname)s, dest=%(destemail)s, sender=%(hostname)s]
maxretry = 2


[apache-auth-1]

enabled  = true
filter   = apache-auth
logpath  = /Library/FileMaker Server/HTTPServer/logs/error_log
action   = pf[name=apache-auth, port="http,https"]
           sendmail-whois[name=%(hostname)s, dest=%(destemail)s, sender=%(hostname)s]
maxretry = 4

[apache-auth-2]

enabled  = true
filter   = apache-auth
logpath  = /Library/FileMaker Server/HTTPServer/logs/ssl_error_log
action   = pf[name=apache-auth, port="http,https"]
           sendmail-whois[name=%(hostname)s, dest=%(destemail)s, sender=%(hostname)s]
maxretry = 4


[apache-noscript-1]

enabled  = true
action   = pf[name=apache-noscript, port="http,https,16000"]
           sendmail-whois[name=%(hostname)s, dest=%(destemail)s, sender=%(hostname)s]
filter   = apache-noscript
logpath  = /Library/FileMaker Server/HTTPServer/logs/error_log

[apache-noscript-2]

enabled  = true
action   = pf[name=apache-noscript, port="http,https,16000"]
           sendmail-whois[name=%(hostname)s, dest=%(destemail)s, sender=%(hostname)s]
filter   = apache-noscript
logpath  = /Library/FileMaker Server/HTTPServer/logs/ssl_error_log
```


#### Reference

For fail2ban documentation, see http://www.fail2ban.org.

Our original blog post is at http://blog.beezwax.net/2013/05/06/fail2ban-with-filemaker-server/ and provides some background on the initial implementation.
One recent change that dates the blog however is that ipfw is no longer available as of Mac OS 10.10 (Yosemite), so you must use pf as your action/banaction.

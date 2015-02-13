## filemaker-fail2ban
#### Fail2ban rules for FileMaker Server

These files files can be added to a fail2ban installment so that the relevant FileMaker Server logs will be included.

To install the fail2ban core files, you can use the source at fail2ban.org, or a Mac OS package manager like MacPorts or Homebrew.

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

#### Reference

For fail2ban documentation, see http://www.fail2ban.org.

Our original blog post is at http://blog.beezwax.net/2013/05/06/fail2ban-with-filemaker-server/ and provides some background on the initial implementation.
One recent change that dates the blog however is that ipfw is no longer available as of Mac OS 10.10 (Yosemite), so you must use pf as your action/banaction.

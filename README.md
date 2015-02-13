# filemaker-fail2ban
fail2ban rules for FileMaker Server

These files are not a full set of files for fail2ban, merely files that can be added to a fail2ban installment so that the relevant FileMaker Server logs can be included.

To install the fail2ban core files, you can use the source at fail2ban.org, or a Mac OS package manager like MacPorts or Homebrew.

Additionally, you'll need to add something like the following into your jail.local file (which you may need to create):

```
[filemaker-client]

enabled = true
port    = 5003,16001,http,https
filter  = filemaker-client
logpath  = /Library/FileMaker Server/Logs/Event.log
maxretry = 6
```

REFERENCE

For fail2ban documentation, see http://www.fail2ban.org.

Our original blog post is at http://blog.beezwax.net/2013/05/06/fail2ban-with-filemaker-server/ and provides some background on the initial implementation.

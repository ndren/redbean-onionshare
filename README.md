# redbean-onionshare
OnionShare; no Flask, just redbean.

[Actually Portable Executables](https://justine.lol/ape.html) are very cool. Not least because they are the same binary compatible with 6 Operating Systems at the same time, from NetBSD to Linux to Windows, but also because they do so while taking up less than a megabyte of space. Of particular interest here is [redbean](https://justine.lol/redbean/index.html), a file server that is also a zip archive and also implements Lua support.

Now, I am not an expert, and a minute ago I was wondering how to remove files from redbean after adding them with zip (zip -d, confirms the manual page). But I do love doing things minimally without ever touching the root user (maybe except for installing the tor package, but nothing is preventing this from working from a [prebuilt archive](https://dist.torproject.org/)), and I will try to present this as simply as possible by reimplementing the primary feature in OnionShare - file sharing.

Can I guarantee security? No. Can you delete this trivially afterwards since the root user is never touched? Definitely. See the repository for the files after completion.

Before starting, my personal thanks to Justine for inviting me to 'spread the word'. Your word of encouragement has brought me here :)

Step 0: Set up a folder for your server.

`mkdir onionshare-but-not-really`

Step 1: Get redbean.

```
curl https://justine.lol/redbean/redbean-latest.com >redbean.com
chmod +x redbean.com
```

Step 2: Copy the default Tor configuration.

`cp /etc/tor/torrc .`

Step 3: Disable logging in as `tor`, and use `$USER` instead. (Prevents the need for access to the `root` user, in OnionShare fashion.)

`#User tor`

Step 4: Set up directories for hidden services. Optionally replace $HOME with an absolute path.

```
HiddenServiceDir $HOME/onionshare-but-not-really/hidden_service/
HiddenServicePort 80 127.0.0.1:8080 # redbean uses port 8080. HTTP uses port 80.
DataDirectory $HOME/onionshare-but-not-really/tor/
```

Step 5: (Optional, Recommended) Disable SOCKS. (Does not affect the server.)

`SOCKSPort 0`

Step 6: (Optional) Run tor as daemon. (Requires absolute paths instead of $HOME.)

`RunAsDaemon 1 # Could also use --runasdaemon 1.`

Step 6: Add the relevant files to redbean.

```
zip redbean.com dump
zip -d dump redbean.com # To remove dump.
```

Step 7: Run the server!
```
./redbean -vv
tor -f torrc
```

Step 8: See if it worked!
```
torbrowser-launcher
# Open the URL stored in hidden_service/hostname.
```

Is OnionShare simpler? Probably. Could I have used a [Pomf instance](https://status.uguu.se/clones.html)? Certainly. But it seems there is some beauty in self-hosting and self-doing that cannot be attained otherwise, so here I am :)

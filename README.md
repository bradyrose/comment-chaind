commentchain - p2p microblogging
===========================

<http://www.commentchain.net.co>

Bitcoin Copyright (c) 2009-2013 Bitcoin Developers  
libtorrent Copyright (c) 2003 - 2007, Arvid Norberg  
commentchain Copyright (c) 2013 Miguel Freitas  

What is commentchain?
----------------

commentchain is an experimental peer-to-peer microblogging software.

User registration and authentication is provided by a bitcoin-like network, so
it is completely distributed (does not depend on any central authority).

Post distribution uses kademlia DHT network and bittorrent-like swarms, both
are provided by libtorrent.

Both Bitcoin and libtorrent versions included here are highly patched and do
not interoperate with existing networks (on purpose).

Compiling
----------------
You can build commentchain using these docs: 

[Ubuntu/Debian](https://github.com/miguelfreitas/commentchain-core/blob/master/doc/building-on-ubuntu-debian.md)

[Mac OS X](https://github.com/miguelfreitas/commentchain-core/blob/master/doc/build-osx.md)

[Windows (untested)](https://github.com/miguelfreitas/commentchain-core/wiki/Compiling-for-Windows)

[Unix](https://github.com/miguelfreitas/commentchain-core/blob/master/doc/build-unix.md)

License
-------

Bitcoin is released under the terms of the MIT license. See `COPYING` for more
information or see http://opensource.org/licenses/MIT.

libtorrent is released under the BSD-license.

commentchain specific code is released under the MIT license or BSD, you choose.
(it shouldn't matter anyway, except for the "non-endorsement clause").

Development process
-------------------

There is no development process defined yet.

Developers of either bitcoin or libtorrent are welcomed and will be granted
immediate write-access to the repository (a small retribution for
bastardizing their codebases).

Testing
-------

Some security checks are disabled (temporarily) allowing multiple clients per IP.
Therefore it is possible to run multiple commentchaind instances at the same machine:

    $ commentchaind -datadir=/tmp/commentchain1 -port=30001 -daemon -rpcuser=user -rpcpassword=pwd -rpcallowip=127.0.0.1 -rpcport=40001
    $ commentchaind -datadir=/tmp/commentchain2 -port=30002 -daemon -rpcuser=user -rpcpassword=pwd -rpcallowip=127.0.0.1 -rpcport=40002
    $ commentchaind -rpcuser=user -rpcpassword=pwd -rpcallowip=127.0.0.1 -rpcport=40001 addnode <external-ip>:30002 onetry

Note: some features (like block generation and dht put/get) do now work unless
the network has at least two nodes, like these two instances in the example above.

Wire protocol
-------------

Bitcoin and libtorrent protocol signatures have been changed on purpose to
make commentchain network incompatible. This avoids the so called 
["merge bug"](http://blog.notdot.net/2008/6/Nearly-all-DHT-implementations-vulnerable-to-merge-bug).

- Bitcoin signature changed from "f9 be b4 d9" to "f0 da bb d2".
- Bitcoin port changed from 8333 to 28333.
- Torrent signature changed from "BitTorrent protocol" to "commentchain protocollll".
- Torrent/DHT query changed from "y" to "z"
- Torrent/DHT answer changed from "a" to "x"

Quick JSON command examples
---------------------------

In order to use JSON-RPC you must set user/password/port by either command
line or configuration file. This is the same as in [bitcoin](https://en.bitcoin.it/wiki/Running_Bitcoin)
except that commentchain config file is `/home/user/.commentchain/commentchain.conf`

To create a new (local) user key:

    ./commentchaind createwalletuser myname

This command returns the secret which can be used to recreate the key in a
different computer (in order to access the account). The user should be
encouraged to make a copy of this information, either by printing, snapshoting
or even writing it down to a piece of paper.

The newly created user only exists in the local database (wallet), so
before the user is able to fully use the system (post messages), his public
key must be propagated to the network:

    ./commentchaind sendnewusertransaction myname

The above command may take a few seconds to run, depending on your CPU. This
is normal.

To create the first (1) public post:

    ./commentchaind newpostmsg myname 1 "hello world"

To add some users to the following list:

    ./commentchaind follow myname '["myname","myfriend"]'

To get the last 5 posts from the users we follow:

    ./commentchaind getposts 5 '[{"username":"myname"},{"username":"myfriend"}]'

To send a new (private) direct message:

    ./commentchaind newdirectmsg myname 2 myfriend "secret message"

Notes for `newdirectmsg`:

- The post number (2) follows the same numbering as `newpostmsg`, make
sure they don't clash.

- The recipient must be your follower.

To get the last 10 direct messages to/from remote user:

    ./commentchaind getdirectmsgs myname 10 '[{"username":"myfriend"}]'

Notes for `getdirectmsgs`:

- These direct message IDs (max_id, since_id etc) are not related to post
numbers. The numbering is local and specific to this thread.

- This function will return messages which have been successfully decrypted
upon receiving or that have been sent by this same computer. A different
computer, sharing the same account, will see the same received, but not the
same sent messages.

To setup your profile:

    ./commentchaind dhtput myname profile s '{"fullname":"My Name","bio":"just another user","location":"nowhere","url":"commentchain.net.co"}' myname 1

Note: increase the revision number (the last parameter) whenever you want to
update something using dhtput.

To obtain the profile of another user:

    ./commentchaind dhtget myfriend profile s

To obtain the full list of commands

    ./commentchaind help


Running the web interface
-------------------------

First you'll need to grab the latest version of the web UI code and put it
in your commentchain data dir:

    cd ~/.commentchain/
    git clone https://github.com/miguelfreitas/commentchain-html.git ./html

Next, run the commentchain daemon. The RPC username and password are currently
hard coded as "user" and "pwd" in the web client so you'll need to specify
them:

    ./commentchaind -rpcuser=user -rpcpassword=pwd -rpcallowip=127.0.0.1

Visit [http://localhost:28332/index.html](http://localhost:28332/index.html)
in your web browser and you should see a page asking you to choose between the
Desktop and Mobile interfaces.

Different themes
-------------------------

If you prefer new modern look of commentchain with new untested things, you can try commentchain-calm theme
But be careful, it is in beta stage.

    cd ~/.commentchain/
    git clone https://github.com/iHedgehog/commentchain-calm.git ./html

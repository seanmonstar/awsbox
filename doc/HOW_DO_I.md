# How Do I XXX in awsbox?

This is a cookbook of how to Get Common Things Done with
awsbox.

## How Do I Push Code?

If you use awsbox from an existing project, a git "remote" will
be added for you with the name of the machine you chose (with the
-n flag).

## How Do I SSH into my VM?

You can SSH as the user that runs your app:

    $ ssh app@[ip address]

Or as an admin user with sudo privileges

    $ ssh ec2-user@[ip address]

## How Do I Check My Server Logs?

    $ ssh app@[ip address] 'tail -F var/log/*'

## How Do I Restart The Web Services On My Server?

    $ ssh app@[ip address] 'forever restartall'

## How Do I Give Other People Access to the VM?

Add their key to `~ec2-user/.ssh/authorized_keys` to give access to
administer the machine and the app.

Alternately, to only give app access, turn the symlink in
`~app/.ssh/authorized_keys` to a real file, and add your buddy's key
there:

    $ cp -L ~/.ssh/authorized_keys ~/.ssh/foo && mv .ssh/foo .ssh/authorized_keys
    $ # now add the key

## How Do I Enable SSL?

SSL is already enabled with a self signed SSL certificate.  You can update the
SSL credentials thusly:

    $ scp myprivatekey.pem proxy@[ip address]:key.pem
    $ scp mycert.pem proxy@[ip address]:cert.pem
    $ ssh proxy@[ip address] 'forever restartall'

## How Do I Disable SSL?

At creation time you can pass `--ssl=disable` to create the instance with SSL
disabled.

Post creation, you can SSH in as the proxy user, and update config.json to contain:

    { "ssl": "disable" }

Then restart the proxy with `forever restartall`.

## How Do I Force Connections to use SSL?

You can always use HTTP headers (Strict-Transport-Security), but even then, a user's
first request will not be encrypted.  To cause all HTTP traffic to be redirected
to HTTPS, you can SSH in as the proxy user and update config.json to contain:

    { "ssl": "force" }

Then restart the proxy with `forever restartall`.

## How Do I Use WebSockets?

Because we use [http-proxy] for HTTP forwarding, it should Just Work.  Have a look at the
[socket.io example] for a tiny focused example which uses WebSockets via the excellent
socket.io library.

  [http-proxy]: https://github.com/nodejitsu/node-http-proxy
  [socket.io example]: https://github.com/lloyd/awsbox-socketio-example

## How Do I Install Software?

Use yum.

    $ ssh ec2-user@[ip address]
    $ yum search memcached
    $ yum install memcached.x86_64


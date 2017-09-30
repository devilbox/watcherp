# watcherp

[![Build Status](https://travis-ci.org/devilbox/watcherp.svg?branch=master)](https://travis-ci.org/devilbox/watcherp)

**[watcherp](https://github.com/devilbox/watcherp/blob/master/watcherp)** will watch for port binding changes (ports started or stopped binding on a network addresss) and will execute specified commands or shell scripts (`-a`, `-d`) depending on the event.
Once all events have happened during one round (`-i`), a trigger command can be executed (`-t`).
Note, the trigger command will only be execute when at least one add or delete command has succeeded with exit code 0.

---

If you need the same functionality to monitor file system changes, check out **[watcherd](https://github.com/devilbox/watcherd)**.

---

### Modes

**[watcherp](https://github.com/devilbox/watcherp/blob/master/watcherp)** can either use [netstat](https://linux.die.net/man/8/netstat) or [ss](https://linux.die.net/man/8/ss) to watch for binding port changes. The default is to use netstat.

### Placeholders

There are two placeholders available that make it easier to use custom commands/scripts for the add (`-a`) or delete (`-d`) action.:

* `%p` Port that started or stopped binding
* `%n` The network address the port started or stopped binding to

You can specify the placeholders as many times as you want. See the following example section for usage.

### Examples

By using **[vhost_gen.py](https://github.com/devilbox/vhost-gen)** (which is capable of creating Nginx or Apache vhost config files for normal vhosts or reverse proxies), the following example will create new Nginx reverse proxy vhosts on-the-fly. `watcherp` uses its add command to parse network address and port to `vhost_gen.py` which will create a configuration file and reloads Nginx.

```shell
# %n will be replaced by watcherp with the network address the port started binding to
# %p will be replaced by watcherd with the port that started binding
# -p 80 Ignores the web server port itself.
watcherp -v \
  -p 80 \
  -a "vhost_gen.py -r %n:%p -n proxy.%p.example.com -s" \
  -d "rm /etc/nginx/conf.d/%n.conf" \
  -t "nginx -s reload"
```


### Usage

```shell
Usage: watcherp -a <cmd> -d <cmd> [-p <ports> -t <cmd> -w <str> -i <int> -v]
       watcherp --help
       watcherp --version

watcherp will look for port binding changess (added and removed port bindings)
and will execute specified commands or shell scripts (-a, -d) depending on the event.
Once all events have happened during one round (-i), a trigger command can be executed (-t).
Note, the trigger command will only be execute when at least one add or delete command has succeeded with exit code 0.

Required arguments:
  -a <cmd>      Command to execute when a port started to bind on an address.
                You can also append the following placeholders to your command string:
                %p The port that started binding.
                %n The network address the port binded on.
                Example: -a "script.sh -f %p -c %n -a %p"
  -d <cmd>      Command to execute when a binding port was removed from an address.
                You can also append the following placeholders to your command string:
                %p The port that started binding.
                %n The network address the port binded on.
                Example: -b "script.sh -f %p -c %n -a %p"

Optional arguments:
  -p <ports>    Comma separated list of ports to ignore.
  -t <cmd>      Command to execute after all binding ports have been added or removed during one round.
                No argument will be appended.
  -w <str>      The port binding watcher to use. Valid values are:
                'netstat': Uses netstat binary to watch for binding changes.
                'ss':      Uses ss binary to watch for binding changes.
                The default is to use 'netstat' as the watcher.
  -i <int>      Specify the interval in seconds for how often to look for port changes.
  -v            Verbose output.

Misc arguments:
  --help        Show this help screen.
  --version     Show version information.
```

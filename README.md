# watcherp

[![Build Status](https://travis-ci.org/devilbox/watcherp.svg?branch=master)](https://travis-ci.org/devilbox/watcherp)

watcherp will look for listening port changes (added and removed listening ports) and will execute specified commands or shell scripts (`-a`, `-d`) depending on the event.
Once all events have happened during one round (`-i`), a trigger command can be executed (`-t`).
Note, the trigger command will only be execute when at least one add or delete command has succeeded with exit code 0.


### Placeholders

There is one placeholder available that make it easier to use custom commands/scripts for the add (`-a`) or delete (`-d`) action.:

* `%p` Port that was added or removed

You can specify the placeholders as many times as you want. See the following example section for usage.


### Usage

```shell
Usage: watcherp -a <cmd> -d <cmd> [-t <cmd> -i <int> -p <ports> -v]
       watcherp --help
       watcherp --version

watcherp will look for listening port changes (added and removed listening ports)
and will execute specified commands or shell scripts (-a, -d) depending on the event.
Once all events have happened during one round (-i), a trigger command can be executed (-t).
Note, the trigger command will only be execute when at least one add or delete command has succeeded with exit code 0.

Required arguments:
  -a <cmd>      Command to execute when a port was added.
                You can also append the following placeholders to your command string:
                %p The port that was added.
                Example: -a "script.sh -f %p -c %p -a %p"
  -d <cmd>      Command to execute when a port was removed.
                You can also append the following placeholders to your command string:
                %p The port that was removed.
                Example: -b "script.sh -f %p -c %n -a %p"

Optional arguments:
  -p <ports>    Comma separated list of ports to ignore.
  -t <cmd>      Command to execute after all ports have been added or removed during one round.
                No argument will be appended.
  -i <int>      Specify the interval in seconds for how often to look for port changes.
  -v            Verbose output.

Misc arguments:
  --help        Show this help screen.
  --version     Show version information.
```

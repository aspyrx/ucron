# ucron

Runs commands on a schedule.

## Usage

ucron uses a configuration directory to store its state, checked in the
following order:

1. `$UCRON_CONFIG` environment variable
2. `$XDG_CONFIG_HOME/ucron`
3. `$HOME/.config/ucron`

The configuration directory must contain a `ucrontab` file. See below.

Once the `ucrontab` is set up, simply run `ucron` to start the daemon.

### ucrontab

The `ucrontab` consists of key-value settings, followed by `---`, then a
pipe-separated (`|`) schedule table.

```
<option>=<value>
---
<interval>|<command>
```

#### Options

- `LIFETIME`: The maximum lifetime of a ucron process, in seconds.
    - Default: `84600` (1 day)
    - Some systems impose a limit on the maximum lifespan of a process. ucron
      gets around this by starting another instance of itself after the given
      time has elapsed.

#### Schedule

Each schedule entry has an interval in seconds, followed by a command to
execute. After ucron is started, the commands will be run periodically at the
specified interval.

#### Example

The following `ucrontab` would echo `5` every 5 seconds, and `60` every 60
seconds. It would also restart ucron every 20 seconds.

```
LIFETIME=20
---
5|echo "5"
60|echo "60"
```

## Logs

ucron will log diagnostic messages to `$UCRON_CONFIG/ucron.log`.

The standard output and error of the running commands is sent to
`$UCRON_CONFIG/ucron-cmd.log`. Each execution is prefixed with a message
indicating the start time and command, but since ucron does not wait for the
started commands to finish, commands that execute simultaneously may have their
output interleaved.

## Database

ucron maintains a database of pending commands in `$UCRON_CONFIG/ucrondb`. Thus,
changes to `ucrontab` won't be reflected immediately. To regenerate the
database, send a `SIGHUP` to the running ucron process.


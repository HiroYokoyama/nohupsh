# nohupsh

`nohupsh` is a simple shell utility that wraps a given command in a new, timestamped shell script and runs it in the background using `nohup`.

This is useful for running long-lasting commands that you want to persist even after you close your terminal, while also creating a clear log of when the command was started and finished.

## Features

  * **Persistent Execution:** Runs any command in the background using `nohup`, so it continues running after you log out.
  * **Timestamped Script Generation:** Creates a unique, executable `.sh` file for every command you run (e.g., `nohupsh-20251007-153000.sh`).
  * **Built-in Logging:** The generated script automatically logs its start time, end time, hostname, and working directory to its output (which `nohup` typically saves to `nohup.out`).
  * **Chainable (Manual):** The generated script includes a placeholder section, allowing you to manually edit the file and add sequential follow-up commands if needed.

## Prerequisites

This is a Bash script. You'll need:

  * A Unix-like environment (Linux, macOS, etc.)
  * Bash shell

## Installation

1.  Save the script as `nohupsh` (or any name you prefer) in a directory.
2.  Make it executable:
    ```bash
    chmod +x nohupsh
    ```
3.  (Optional) For system-wide use, move it to a directory in your `PATH`:
    ```bash
    sudo mv nohupsh /usr/local/bin/
    ```

## Usage

To run a command, simply pass it as a string argument to `nohupsh`:

```bash
./nohupsh "your-command-here --with --arguments"
```

If you installed it to your `PATH`, you can run it from any directory:

```bash
nohupsh "your-command-here --with --arguments"
```

## How It Works

When you run `nohupsh "command"`:

1.  It generates a unique script name based on the current date and time (e.g., `nohupsh-20251007-153001.sh`).
2.  It writes a new shell script to this file. This new script contains:
      * A "Started" log block.
      * The command you passed as an argument (`$@`).
      * A "Next Command" placeholder section.
      * A "Finished" log block.
3.  It makes this newly created script executable (`chmod +x`).
4.  It executes the new script in the background using `nohup ./nohupsh-20251007-153001.sh &`.

All output from the generated script, including the "Started" and "Finished" logs and any output from your command, will be redirected by `nohup` (usually to a file named `nohup.out` in the directory where you ran the command).

## Example

Imagine you have a long-running Python script you want to execute.

```bash
$ nohupsh "python3 /opt/scripts/process_data.py --input /data/large_dataset.csv --output /data/results.csv"
```

This will immediately create a file like `nohupsh-20251007-153210.sh` and start it in the background. You can safely close your terminal.

To check its progress, you can monitor the `nohup.out` file:

```bash
$ tail -f nohup.out

=============================================================
"!!!!!!!!!!      nohupsh Started      !!!!!!!!!!"
"nohupsh Shell Script = nohupsh-20251007-153210.sh "
"Start time = Tue Oct  7 15:32:10 JST 2025"
"Hostname = my-server.local"
"Present Working Directory = /home/myuser/projects"
"!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!"

(Output from your python script will appear here...)

(Once finished, you will see:)

"!!!!!!!!!!      nohupsh Finished      !!!!!!!!!!"
"End time = Tue Oct  7 15:55:01 JST 2025"
"!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!"
=============================================================
```

## A Note on Generated Files

This utility creates a new `.sh` file in the current directory **every time it is run**. You may want to periodically clean up these `nohupsh-*.sh` script files.

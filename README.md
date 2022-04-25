## Process Compose

[![made-with-Go](https://img.shields.io/badge/Made%20with-Go-1f425f.svg)](https://go.dev/) [![Maintenance](https://img.shields.io/badge/Maintained%3F-yes-green.svg)](https://GitHub.com/Naereen/StrapDown.js/graphs/commit-activity) [![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com) ![Go Report](https://goreportcard.com/badge/github.com/F1bonacc1/process-compose)

Process compose is a lightweight utility for building custom workflows and execution sequences. It is optimized for:

* Parallelizing processes execution
* Defining execution dependencies and order
* Defining recovery policies (restart `on-failure`, `always`, `no`)
* Declaring processes arguments
* Declaring processes environment variables

It is heavily inspired by [docker-compose](https://github.com/docker/compose), but without the need for containers. The configuration syntax tries to follow the docker-compose specifications, with a few minor additions and lots of subtractions.

### Installation

- Go to the [releases](https://github.com/F1bonacc1/process-compose/releases/latest), download the package for your OS, and copy the binary to somewhere on your PATH. 

### Documentation

* See [examples](https://github.com/F1bonacc1/process-compose/tree/main/examples) of workflows for best practices
* See below



#### List of Features and Planned Features

##### ✅ Mostly implemented

##### ❌ Implementation not started (Your feedback and ⭐ will motivate further development 😃)



#### ✅ <u>Launcher</u>

##### ✅ Parallel

```yaml
process1:
  command: "sleep 3"
process2:
  command: "sleep 3"
```

##### ✅ Serial

```yaml
process1:
  command: "sleep 3"
  depends_on:
    process2: 
      condition: process_completed_successfully # or "process_completed" if you don't care about errors
process2:
  command: "sleep 3"
  depends_on:
    process3: 
      condition: process_completed_successfully # or "process_completed" if you don't care about errors
```

##### ❌ Instance Number

##### ✅ Define process dependencies

```yaml
process2:
  depends_on:
  process2: 
    condition: process_completed_successfully # or "process_started" (default)
  process3: 
    condition: process_completed_successfully
```



#### ✅ <u>Output Handling</u>

##### ✅ Show process name

##### ✅ Different colors per process

##### ✅ StdErr is printed in Red

<img src="./imgs/output.png" alt="output" style="zoom:50%;" />

##### ❌ Silence specific processes



#### ✅ <u>Logger</u>

##### ✅ Per Process Log Collection

```yaml
process2:
  log_location: ./pc.process2.log #if undefined or empty no logs will be saved
```

##### ✅ Capture StdOut output

##### ✅ Capture StdErr output

##### ✅ Merge into a single file

```yaml
processes:
  process2:
    command: "chmod 666 /path/to/file"
environment:
  - 'ABC=42'
log_location: ./pc.global.log #if undefined or empty no logs will be saved (if also not defined per process)
```

##### ❌ Silence specific processes

##### ✅ Process compose console log level

```yaml
log_level: info # other options: "trace", "debug", "info", "warn", "error", "fatal", "panic"
processes:
  process2:
    command: "chmod 666 /path/to/file"
```

This setting controls the `process-compose` log level. The processes log level should be defined inside the process. It is recommended to support its definition with an environment variable that can be defined in `process-compose.yaml`



#### ❌ <u>Health Checks</u>

##### ❌ Is Alive

##### ❌ Is Ready

##### ❌ Auto Restart if not healthy

##### ✅ Auto Restart on exit

```yaml
process2:
  availability:
    restart: on-failure # other options: "always", "no" (default)
    backoff_seconds: 2  # default: 1
    max_restarts: 5 # default: 0 (unlimited)
```



#### ✅ <u>Environment Variables</u>

##### ✅ Per Process

```yaml
process2:
  environment:
    - 'I_AM_LOCAL_EV=42'
```

##### ✅ Global

```yaml
processes:
  process2:
    command: "chmod 666 /path/to/file"
  environment:
    - 'I_AM_LOCAL_EV=42'		
environment:
  - 'I_AM_GLOBAL_EV=42'
```



#### ❌ <u>System Variables</u>

##### ❌ Process replica number

##### ❌ Monitoring

##### ❌ REST API



#### ✅ <u>Configuration</u>

##### ✅ Support .env file

##### ✅ Override ${var} and $var from environment variables or .env values

##### ❌ Merge 2 or more configuration files with override values

##### ✅ Specify which configuration files to use

```shell
process-compose -f "path/to/process-compose-file.yaml"
```

##### ✅ Auto discover configuration files

The following discovery order is used: `compose.yml, compose.yaml, process-compose.yml, process-compose.yaml`. If multiple files are present the first one will be used.



#### ✅ <u>Multi-platform</u>

##### ✅ Linux

The default backend is `bash`. You can define a different backend with a `SHELL` environment variable.

##### ✅ Windows

The default backend is `cmd`. You can define a different backend with a `SHELL` environment variable.

```yaml
  process1:
    command: "python -c print(str(40+2))" 
    #note that the same command for bash/zsh would look like: "python -c 'print(str(40+2))'"
```

Using `powershell` backend had some funky behaviour (like missing `command1 && command2` functionality in older versions). If you need to run powershell scripts, use the following syntax:

```yaml
  process2:
    command: "powershell.exe ./test.ps1 arg1 arg2 argN"
```

##### ❌ macOS
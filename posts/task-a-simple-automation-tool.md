---
title: Task - A simple tool to automate boring jobs
description: At Shamaazi we've been using a tool called `task`. It's incredibly powerful and incredibly useful for organising and performing repeated jobs. Let's explore what it's capable of.
date: 2020-09-24
layout: layouts/post.njk
image: /img/remote/thinking.webp
---

At Shamaazi we've been using a tool called `task`. It's an incredibly powerful tool that can completely
replace Makefiles, or complicated scripts, with a much simpler and arguably more powerful alternative. Outside of that,
it's an incredibly useful organiser for all command-line related activities. At Shamaazi we have a monolithic codebase,
containing 7 different UIs, hundreds of services and all our infrastructure provisioning. We use `task` to manage all
of this, as well as performing housekeeping jobs such as deleting user data when requested or changing peoples contact
addresses. Let's have a quick explore of what `task` is, and what it's capable of.

## Getting Started

The simplest way to install `task` is through an install script they provide.
```bash
curl -sL https://taskfile.dev/install.sh | sh
```
However, there are a tonne of other methods to install it, such as through `brew`, `snap` or `scoop`. You can find them
all [here](https://taskfile.dev/#/installation).

Once installed we can run `task --init` in a directory we want to issue commands from. This will create a simple
`Taskfile.yml` file. This file is used to define all the possible tasks we want to run. Initially, it just contains a
`Hello, World!` example.
```yaml
# https://taskfile.dev

version: '3'

vars:
  GREETING: Hello, World!

tasks:
  default
    cmds:
{% raw %}      - echo "{{.GREETING}}"{% endraw %}
    silent: true
```

Running `task` (or `task default`) will run the `default` task defined above, printing `Hello, World!`. We can break
down the file into some clear sections:
- `version: '3'` - this defines the version of Taskfile to use. We don't need to pay too much attention, but this
    prevents and future releases from stopping your tasks from working.
- `vars:` - this section defines any globally accessible variables we want to use. We can see a single variable,
    `GREETING` defined as the `Hello, World!`. These variables are really powerful, and can reference other variables,
    or can be derived entirely from the output of a command.
- `tasks:` - this section is where the actual tasks are defined. At the moment we just have a single task called
    `default`. When this task is run, it will run the command `echo {%raw%}"{{.GREETING}}"{%endraw%}`. The `silent: true` line simply
    prevents `task` from printing out the command that is being run.

This serves a super quick introduction. But let's cover some of the more powerful features.

## Variables

In the previous section, I mention that the GREETING variable could be derived from the output of a command. This is
sometimes incredibly useful for deriving information that isn't immediately available.
For a quick example of this, let's change the `vars` section to the following:
```yaml
vars:
  GREETING:
    sh: echo "Hello, $(whoami)!"
```
Running `task` now will output `Hello, dglsparsons!` (or whatever your username happens to be!). As it's executing a
command, this could literally be anything. Let's use `wttr.in` to provide the weather (and using
[jq](https://stedolan.github.io/jq/) to quickly make something of the output. We can then add this to a second task.
```yaml
vars:
  GREETING:
    sh: echo "Hello, $(whoami)!"
  WEATHER:
    sh: curl -s wttr.in?format=j1 | jq -r .current_condition[0].weatherDesc[0].value

tasks:
  default:
    cmds:
{%raw%}      - echo "{{.GREETING}}"{%endraw%}
    silent: true
  weather:
    cmds:
{%raw%}      - echo "There be {{.WEATHER}}"{%endraw%}
    silent: true
```
Running `task` now will still print out the same greeting. However, running `task weather` will print out something along the lines of:
```
There be Haze.
```

That was quick and easy. And now we've got that command saved for good, in a nice memorable location.

## Documentation

So our tasks are useful, but they would be a lot more useful if they explained what they did. Let's add some short
descriptions to them. This can be done through the `desc` key on each task.
```yaml
tasks:
  default:
    desc: Prints a greeting.
    cmds:
{%raw%}      - echo "{{.GREETING}}"{%endraw%}
    silent: true
  weather:
    desc: Prints out the current weather.
    cmds:
{%raw%}      - echo "There be {{.WEATHER}}"{%endraw%}
    silent: true
```
We can now run `task -l` or `task --list` to show a handy summary of all the available tasks.
```yaml
$ task --list
task: Available tasks for this project:
* default: 	Prints a greeting.
* weather: 	Prints out the current weather.
```
This makes the tasks much easier to remember in the future!

## Dependencies

Rather than going and downloading a weather forecast every single we want to check, let's create a task to
write the weather forecast into a file.
```yaml
vars:
  GREETING:
    sh: echo "Hello, $(whoami)!"
  WEATHER_FILE: weather.json

tasks:
  default:
    desc: Prints a greeting.
    cmds:
{%raw%}      - echo "{{.GREETING}}"{%endraw%}
    silent: true
  download-weather:
    desc: Downloads a weather forecast into a file
    cmds:
{%raw%}      - curl -s wttr.in?format=j1 > {{.WEATHER_FILE}}{%endraw%}
```
This is a good start, but running `download-weather` will always download the forecast. If we were using some
file as an input, you could set this as a `source`, even with a wildcard. This is incredibly useful for building code
only when required. e.g.
```yaml
tasks:
  build:
    cmds:
      - go build .
    sources:
      - ./*.go
```
This will only run `go build` if any `.go` files have been updated. For our purposes though, we don't have input files. Instead, we can use the `status` field to check programatically.

```yaml
  download-weather:
    desc: Downloads a weather forecast into a file
    cmds:
{%raw%}      - curl -s wttr.in?format=j1 > {{.WEATHER_FILE}}{%endraw%}
    status:
{%raw%}      - test -f ./{{.WEATHER_FILE}}{%endraw%}
```

Running `task download-weather` multiple times will result in the file being downloaded the first time, but not
subsequently. Instead, a message is produced: `task: Task "download-weather" is up to date`.

Let's go one step further and make our previous `weather` task depend on the weather file being downloaded. This can be
done easily through a `deps` field. This means
running the `weather` command would attempt to run `download-weather`. download-weather, in turn, will download the
weather into a file, but, only if the file isn't already present... This sounds a mouthful, but bear with me and you'll hopefully see the value in
this!
```yaml
  weather:
    desc: Prints out the current weather.
    deps:
      - download-weather
    cmds:
{%raw%}      - echo "There be $(cat {{.WEATHER_FILE}} | jq -r .current_condition[0].weatherDesc[0].value)"{%endraw%}
    silent: true
```

Running `task weather` will produce the following output if there is weather to download:
```bash
task: curl -s wttr.in?format=j1 > weather.json
There be Haze
```
However, running it again will not download anything, and just print the value of the weather out:
```bash
task: Task "download-weather" is up to date
There be Haze
```

We can now hopefully see the value in this! We only do work if we have to, and each task can easily check if it has work
to do. This can be incredibly useful for software development. For example, we could create a `deploy` task that depends on a
`build` task. The `build` task will only build if the code has been updated since the last `build`. We can even make the
`deploy` only perform an actual deployment if the built files are newer than the last deployment.

## Conclusion

Through this short guide, we've built a very clever, but easy to read and follow, set of tasks. These tasks are capable
of documenting themselves, allowing them to be easily read and understood. Additionally, the `status` and
`sources` fields can be used to create tasks that only perform actions when they need to. We can also chain these tasks
together through the `deps` field. Chaining tasks in this manner can easily optimize a previously difficult task by
breaking it into component parts, and skipping any parts that do not need to be executed.

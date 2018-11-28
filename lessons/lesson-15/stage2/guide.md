## Event-Driven Network Automation with StackStorm

---

### Part 2 - Actions

Though it's important to understand that StackStorm is all about event-driven automation, it's also useful to spend some time talking about what StackStorm can **do**. Being able to watch for all the events in the world isn't very useful if you can't do anything about what you see. In StackStorm, we can accomplish such things through "[Actions](https://docs.stackstorm.com/actions.html)". Some examples include:

- Push a configuration change to a network device
- Restart a service on a server
- Retrieve information about a virtual machine
- Bounce a switchport
- Send a message to Slack

There are many others - and the list is growing all the time in the StackStorm [Exchange](https://exchange.stackstorm.org/). In short, Actions can be thought of simply as neatly contained bits of code to perform a task. They accept input, do work, and usually provide some output.

Many of the `st2` subcommands we saw in the previous lab use verbs like `get`, `create`, `delete`, `list` for their corresponding resources.
For instance, to list the available actions that are currently present on our system, we can run:

```
st2 action list
```
<button type="button" class="btn btn-primary btn-sm" onclick="runSnippetInTab('st2', 0)">Run this snippet</button>

You can see that each action is located within a "pack", which we explained in the last lab. For instance, the "core" pack comes pre-installed with StackStorm,
and contains many common actions for doing simple things like running bash commands, calling a REST API, and more.


#### Hello World!

The `local` action in the `core` pack is referred to by it's "full name" as `core.local`. We can pass this to `st2 run` to execute this action:

```
st2 run core.local "echo Hello World!"
```
<button type="button" class="btn btn-primary btn-sm" onclick="runSnippetInTab('st2', 1)">Run this snippet</button>

Not every action is created equal - like any program or script, each action comes with different parameters - some optional, some required - that are necessary for it
to get its job done. Fortunately, we can use the handy `-h` flag to gain insight right at the command line into what parameters any action supports:

```
st2 run core.local -h
```
<button type="button" class="btn btn-primary btn-sm" onclick="runSnippetInTab('st2', 2)">Run this snippet</button>

You can see that the `cmd` parameter is required, and is what we supplied in double quotes - the command to run on the bash shell.

In StackStorm, the term "execution" is used to describe an instance of a running action. Each time you run an action, it's given an execution ID, which is a unique identifier of that exact instance where that action was run. Note the ID in the output of the command you just ran. You can use this ID to retrieve this same detail at any time:

    st2 execution get < EXECUTION ID >

This is useful because, as we'll see in the next few labs, we don't always run actions on the command-line directly like this. Sometimes it's done for us by a rule, or a workflow. In those cases, retrieving execution details using `st2 execution get` is often the only way to know how an action performed. We can see a list of recent executions as well:

```
st2 execution list
```
<button type="button" class="btn btn-primary btn-sm" onclick="runSnippetInTab('st2', 4)">Run this snippet</button>

Now, this is a simple example, and there's way more to get into in the `core` pack, such as executing remote commands or scripts, calling REST APIs, and more. However,
this is a network automation lesson, and there's way too much value in using a tool like StackStorm for event-driven network automation, so let's get
into some more relevant examples.

#### Running NAPALM Actions against network devices

You may have seen [in another lesson](https://labs.networkreliability.engineering/labs/?lessonId=13&lessonStage=1) that
[NAPALM](https://github.com/napalm-automation/napalm) is an extremely useful tool in network automation. It's a Python library that sits on top of many vendor-specific
APIs like Juniper's NETCONF, Arista's eAPI, and Cisco's NX-API and exposes common functions so you don't have to worry about these vendor's APIs. You just interact
with NAPALM in your Python scripts, and NAPALM takes care of communicating with the upstream device(s).

StackStorm's exchange includes a pack for exposing NAPALM's functionality. This means, among other things, we can use NAPALM functions as StackStorm actions.
You might have noticed earlier that this pack is already installed, and the actions in the pack are available to us:

```
st2 action list --pack=napalm
```
<button type="button" class="btn btn-primary btn-sm" onclick="runSnippetInTab('st2', 5)">Run this snippet</button>


The `get_facts` action in this pack is a great place to get started. Like we saw in the NAPALM lesson, this action gathers some basic information about a network device, like the serial number and hostname:

```
st2 run napalm.get_facts hostname=vqfx1
```
<button type="button" class="btn btn-primary btn-sm" onclick="runSnippetInTab('st2', 6)">Run this snippet</button>

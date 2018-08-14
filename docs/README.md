# ACR Task

A task config consists of steps. Each step is tailored to an independent container and describes how the task execution can interact with the container.

```yaml
stepTimeout: int
steps:
  - cmd: string
  ...
  - cmd: string
push:
  - string
  - string
```

* [Task Properties](#Task-Properties) apply to the entire task or all steps in a task, like timeouts.  
* [Step Properties](#step-properties) define the behavior of a single step in the entire task and how they interact with other steps. 
* [Template Values](templates.md) can be used in a task config file. 

## Context

Azure Container Builder can freely flow and manipulate context between Steps. It does this by creating a default workspace for each build request and storing artifacts in the workspace after each Step. This means a Step can access any artifact produced from an early Step.

## Composing steps together

Azure Container Builder is able to chain steps together to allow parallel and sequential execution from an `acb.yaml` template. It does this by creating a consistent DAG based on all Steps' `when` property. Unless `when` is specified as `-` it will not execute steps in parallel, it will assume all steps should proceed sequentially. In order for a Step B to block for a Step A in a sequential matter, it should use `when: ['A']`. Azure Container Builder reproduces the same DAG in a deterministic manner.

```yaml
stepTimeout: int 
totalTimeout: int 
push: [string, string, ...]

steps:
  - id: someID

...

secrets:
    secretEnv: string (optional)
```



## Task Properties

- [secrets](#secrets)
- [stepTimeout](#steptimeout)
- [totalTimeout](#totaltimeout)
- [push](#push)

### secrets

The decrypted value is set as the field specified in `secretEnv` which can be reference in scripts via `secretEnvs`

### stepTimeout

`stepTimeout` can be used to set the maximum time a step has to execute. This property can be overridden by a particular Step's individual `timeout` property.

### totalTimeout

`totalTimeout` can be used to set the maximum time all steps must execute within.

### push

`push` is an optional list of images and tags to push after the build has completed. This is a shortcut to creating multiple `push` commands after `build`s.


## Step properties

Step properties defines the execution characteristic as well as the interaction model with other steps and the container of that step. 

```yaml
# An example Step
## A Step describes how to interact with a container.
## It consists of the following properties:

id: string (optional)
cmd: string (required)
workDir: string (optional)
entryPoint: string (optional)
envs: [string, string, ...] (optional)
secretEnvs: [string, string, ...] (optional)
ports: [string, string, ...] (optional)
when: [string, string, ...] (optional)
exitedWith: [int, int, ...] (optional)
exitedWithout: [int, int, ...] (optional)
timeout: int (in seconds) (optional)
rm: bool (optional)
detach: bool (optional)
startDelay: int (in seconds) (optional)
```

For details on each specific property in a Step, follow these links:

- [id](#id)
- [cmd](#cmd)
- [workDir](#workdir)
- [entryPoint](#entrypoint)
- [envs](#envs)
- [secretEnvs](#secretenvs)
- [ports](#ports)
- [when](#when)
- [exitedWith](#exitedwith)
- [exitedWithout](#exitedwithout)
- [timeout](#timeout)
- [rm](#rm)
- [detach](#deatch)
- [startDelay](#startdelay)

### id

The `id` property is a unique identifier to reference the step throughout the task.

### cmd

The `cmd` property of a step specifies which image to use when running the operation as well as any additional command-line parameters.

### workDir

`workDir` can be used to set a working directory when executing a step. By default, Azure Container Builder will produce a default root directory as the working directory. However, if your build has more than one step, you can share the artifacts created from previous steps.

### entryPoint

`entryPoint` overrides the entry point of a step's container.

### envs

`envs` sets environment variables for a step.

### secretEnvs

`secretEnvs` is a list of environment variables which are encrypted using Azure Key Vault. These values are decrypted using the [secrets](#secrets) property.

### ports

`ports` is a list of ports to publish to the host.

### timeout

`timeout` is the maximum duration for a step to execute.

### rm

`rm` determines whether or not the step's container should be removed after execution.

### detach

`detach` determines whether or not the container should be detached when running.

### when

`when` is used to block a Step's execution until one or more other Steps have been completed.

### exitedWith

`exitedWith` can be used to trigger a task when previous steps exited with one or more of the specified exit codes.

### exitedWithout

`exitedWithout` can be used to trigger a task when previous steps exited without one or more of the specified exit codes.

### startDelay

`startDelay` can be used to delay a step's execution. This is an integer value measured in seconds.


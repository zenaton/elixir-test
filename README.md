# elixir-test

## Zenaton Wait project

### Purpose

The purpose of this exercise is to test out your capacity to solve a problem. It's possible that you have never coded in Elixir nor a functional programming language before but don't worry, we will take this into account.

> NB: If you have never worked on Elixir, it is also a good opportunity to know more about the language itself and realize if you want to work with it on a daily basis @ **Zenaton**.

### General acceptance criteria

- All code must be in a `git` repo (you can use your own github account).
- OTP application is a mix project.
- The Engine application name is `:zenaton_engine` (main Elixir module is `ZenatonEngine`).
- Agent application name is `:zenaton_agent` (main Elixir module is `ZenatonAgent`).
- Wait Interface is just a set of public functions of `ZenatonAgent` module.
- You can use any Elixir or Erlang library you want to.
- Code accuracy also matters. A readable, safe, refactorable code is a plus.

### The project

You are going to implement the Zenaton Wait feature like the one provided in the Zenaton SDK, but in Elixir.

If you do not know what I mean by `Zenaton Wait`, you should have a look at this: [page][zenaton-documentation]

You are going to implement two OTP applications
![Wait architecture][wait-architecture]

Agents will send `Wait` requests to the Engine.
The Engine will be responsible to take them and answers back the caller Agent when the `Wait` has been properly done.

Agents **MUST** be able to send as many `Wait` requests as the user wants.

### Steps

*Acknowledgement: I know this test can be complicated, this is why I suggest you to proceed by steps. Each step validated will give you more points for the hiring process.*

1. 1 Agent <-> 1 Engine (no persistence).
2. 1 Agent <-> 1 Engine (with persistence).
3. N Agents <-> 1 Engine (with persistence).

#### Step 1

In this step, we will use only 1 agent and 1 engine.

##### Agent

Agents have a `Wait` public interface the user will use to schedule `wait` requests.
**Eg:**

```elixir
iex(1)> ZenatonAgent.Wait.seconds(30)
iex(2)> ZenatonAgent.Wait.timestamp(1522591200)
iex(3)> ZenatonAgent.Wait.at("15:10:23")
```

Agents **MUST NOT** be connected to a database.

If the Agent is disconnected for any reason, it **MUST NOT** lose its previously requested `Wait`. This means after sending
a wait request, the Agent can be restarted and the Engine **MUST** be able to inform it that the requested wait is over.
If the Agent is still disconnected when the wait ends, the Engine **SHOULD** notify the Agent that the wait is over as soon as the Agent is available again.

##### API reference

Requirements for public functions provided by `ZenatonAgent` module. Any method should return success result or error result.

```elixir
@type agent_error :: {:error, :wrong_arguments | :connection_issue}

@spec seconds(duration :: integer()) :: :ok | agent_error

@spec timestamp(timestamp :: integer()) :: :ok | agent_error

@spec at(time :: binary()) :: :ok | agent_error
```

##### Communication protocol

When you run one of these methods, it **MUST** send a request to the connected engine.

You are totally free to use the communication layer of your choice.

The payload sent to the engine **SHOULD** be:

```elixir
%{
  "duration" => 30         |
  "timestamp" => 1522591200
}
```

##### Engine

The Engine will be responsible to store & execute `Wait` requests.

Multiple implementations of the `Wait` execution are possible. You're free to choose the one you want to do.

In this step, since no persistence is involved, if the Engine stops, whatever the reason is, it won't be able to recover
existing `Wait` executions.

#### Step 2

In this step, we will still use only 1 agent and 1 engine.

##### Engine

This step is about adding persistence to the Engine. This will allow the Engine to be restarted multiple times while
wait executions are running, and 

If the Engine stops for some reasons, it **MUST** recover existing `Wait` executions.

##### Storage

You're free to choose the storage you want.

#### Step 3

In this step, we will use n agents and 1 engine.

##### Engine

The goal is to enable the engine to receive requests coming from n agents. When an agent A sends a Wait request, the
engine will process it as before. When the wait ends, the engine **MUST** notify **ONLY** agent A.

You are free to make any change required in order to make this work.

##### Agent

You are free to make any change required to the agent in order to make this work.

[zenaton-documentation]: https://zenaton.com/documentation/workflow-waiting?lang=php#duration
[wait-architecture]: /img/wait_system.png

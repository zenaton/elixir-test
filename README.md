# elixir-test

## Zenaton Wait project

### Purpose

The purpose of this exercise is to test out your capacity to solve a problem. It's possible that you have never coded in Elixir nor a functional programming language before, don't worry we will take this into account.

> NB: If you have never worked on Elixir, it is also a good opportunity to know more about the language itself and realize if you want to work with it on a daily basis @ **Zenaton**.

### General acceptance criteria

- All code is in `git` repo (candidate can use his/her own github account).
- OTP application is a mix project.
- Engine application name is `:zenaton_engine` (main Elixir module is `ZenatonEngine`).
- Agent application name is `:zenaton_agent` (main Elixir module is `ZenatonAgent`).
- Wait Interface is just set of public functions of `ZenatonAgent`.
- Candidate can use any Elixir or Erlang library he/she wants to.
- Code accuracy also matters. Readable, safe, refactorable code is plus.

### The project

You are going to implement the Zenaton Wait feature like the one provided in Zenaton SDK but in Elixir.

If you do not know what I mean by `Zenaton Wait`, you should have a look at this: [page][zenaton-documentation]

You are going to implement two OTP applications
![Wait architecture][wait-architecture]

Agents will send `Wait` requests to the Engine.
The Engine will be responsible to take them and answers back the caller Agent when the `Wait` has been properly done.

Agents **SHOULD** send as many `Wait` as the user wants.

### Steps

*Acknowledgement: I know this test can be complicated, this is why I suggest your to proceed by steps. Each step validated will give you more points for the hiring process.*

1. 1 Agent <-> 1 Engine (no persistence).
2. 1 Agent <-> 1 Engine (with persistence).
3. N Agents <-> 1 Engine (with persistence).

#### Agent

Agents have a `Wait` public interface where the user will schedule its `Wait`.
**Eg:**

```elixir
iex(1)> ZenatonAgent.Wait.seconds(30)
iex(2)> ZenatonAgent.Wait.timestamp(1522591200)
iex(3)> ZenatonAgent.Wait.at("15:10:23")
```

Agents **MUST NOT** be connected to a database.

If Agents are disconnected for any reasons, they **MUST NOT** lose their previous requested `Wait`.

##### API reference

Requirements for public functions provided by `ZenatonAgent` module. Any method should return success result or error result.

```elixir
@type agent_error :: {:error,
    :wrong_arguments  |
    :connection_issue |
  }
```

@spec seconds(duration :: integer()) :: :ok | agent_error

@spec timestamp(timestamp :: integer()) :: :ok | agent_error

@spec at(time :: binary()) :: :ok | agent_error

##### Communication protocol

When you run one of these methods, it **MUST** send a request to the connected engine.

You are totally free to select the communication layer of your choice.

The payload sent to the engine **SHOULD** be:

```elixir
%{
  "duration" => 30         |
  "timestamp" => 1522591200
}
```

#### Engine

In every steps of the challenge, there will be only one Engine.

Engine will be responsible to store & execute `Wait` requests.

Multiple implementations of the `Wait` execution are possible. You're free to chose the one you want to do.

If the Engine stops for some reasons, it **MUST** recover existing `Wait` executions.

##### Storage

You're totally free to chose the storage you want.

[zenaton-documentation]: https://zenaton.com/documentation/workflow-waiting?lang=php#duration
[wait-architecture]: /img/wait_system.png
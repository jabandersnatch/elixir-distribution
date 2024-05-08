### README: Running Distributed Code with Elixir

This document provides instructions on how to execute the distributed Elixir code for processing data using various tasks defined in the modules `Task1` and `Task2`.

#### Prerequisites

- Elixir 1.9 or higher
- Erlang/OTP 22 or higher
- A configured mix project if you plan to use dependencies such as `Nx`

#### Setup

1. **Clone the Repository**:
   Clone the code repository to your local machine or ensure your code is placed in an appropriate directory structure in your existing project.

2. **Install Dependencies** (if any):
   Run `mix deps.get` in the terminal at the root of your project to install any necessary dependencies specified in your `mix.exs` file.

#### Running the Distributed System

The system uses Erlang's built-in distributed capabilities to perform computations across multiple nodes. Ensure you have multiple nodes set up or simulate them on a single machine using different terminal windows.

1. **Starting IEx Sessions**:
   Open separate terminal sessions for each node. In each session, start an interactive Elixir shell with a named node:

   ```bash
   iex --sname foo@localhost -S mix
   iex --sname bar@localhost -S mix
   iex --sname baz@localhost -S mix
   ```

2. **Connecting Nodes**:
   In each node session (except the first), connect to the other nodes. For example, in `bar@localhost`:

   ```elixir
   Node.connect(:foo@localhost)
   ```

   Repeat this in other sessions to ensure all nodes are interconnected.

3. **Compiling Modules**:
   Ensure all modules (`Distribute`, `Task1`, `Task2`, etc.) are compiled. In each IEx session, run:

   ```elixir
   c("path/to/module.ex")
   ```

4. **Starting the Actor**:
   In one of the node sessions, start the actor from the `Distribute` module:

   ```elixir
   pid = spawn(fn -> Distribute.actor(&Task1.count_para/1, &Task1.union_fun/2, &Task1.partition_fun/2, %{}, :some) end)
   Process.register(pid, :some)
   ```

5. **Initiating the Task**:
   Send the start message from another node to begin processing:

   ```elixir
   Node.spawn(:foo@localhost, fn -> send(:some, {:start, "text to process"}) end)
   ```

   Replace `"text to process"` with actual data or load it from a file using the `ReadFile` module.

#### Monitoring Execution

You can monitor the processing in real-time through IEx sessions. Log statements and process monitoring will show how data is processed and merged back using the specified union function.

#### Troubleshooting

- Ensure all nodes are connected using `Node.list()` which should list all other nodes.
- Check console logs for any error messages or exceptions.
- Make sure Erlang cookies match if you face issues connecting nodes.

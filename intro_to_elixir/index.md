---
title: 'Intro to Elixir'
date: "2019-09-01"
url: "presentations/intro-to-elixir"
ratio: "16:9"
categories:
- Elixir
tags:
- Elixir
description: "An introduction to Elixir"
---
class: center, middle, inverse

# Introduction to Elixir

---
class: left


## A (brief) history of Elixir

.left-column-big[
### Erlang
* Created by Joe Armstrong, Robert Virding, and Mike Williams in 1986 for Ericcson.
* Built to run telcom swiches, so needed to be extremely fault tolerant, distributed, highly available (non stop), hot swappable code.
* Cisco currently uses Erlang and ships about 2M devices a year containing Erlang
* Top 8 ISP's use Erlang based systems to control their networks
* Estimated 90% of all internet traffic passes through Erlang controlled nodes
]

.right-column-small[
  ![Erlang Logo](assets/erlang.png)
]

---
class: left

## A (brief) history of Elixir (cont)
.left-column-big[
### Elixir
* Created by José Valim as an R&D project for his company Plataformatec in 2012
    * 1.0 was released in 2014 and new minor version is released every 6 months or so
* Built to run on the Erlang Virtual Machine, which provides the scalable and fault-tolerant foundation
* Why not just use Erlang?
  * José wanted to bring the developer experience of Ruby to the Erlang world, hence Elixir
]

.right-column-small[
  ![Elixir Logo](assets/elixir.png)
]
???

* José was a Ruby user and contributer.

---
class: left

## A (non-exhasitve) list of companies using Elixir

* Adobe
* Amazon (SimpleDB)
* Bleacher Report
* Change.org
* Discord [How Discord Scaled Elixir to 5,000,000 Concurrent Users](https://blog.discordapp.com/scaling-elixir-f9b8e1e7c29b)
* Facebook
* Heroku
* Pinterest
* Slack
* Toyota Connected
* Pepsi

???

A list of established companies to indicate that Elixir is not a fringe language and is used by mainstream companies.

---
class: left

## Elixir paradigm

.left-column-half[
#### Elixir is a Dynamic Functional Languge.
* variables are immutable
* supports higher-order functions
* everything is an expression
* type checks are done at runtime
]

.right-column-half[
```elixir
# Immutability
e = [1,2,3]
e ++ [4]
# e still equals [1,2,3]

# Higher Order Functions
def times_two(x), do: x * 2

Enum.map([1,2,3], &times_two/1)
# [2,4,6]

# Expressions
result = if (true) do :ok else :error end
# if returns a value, result = :ok
```
]

???

Immutable - data referenced by variable doesn't change, but the varaible can reference different data
Immutable example
 - elixir `e = [1,2,3]` `e ++ [4]` e still equals `[1,2,3]`
 - node `const e = [1,2,3]` `e.push(4)` e now equals `[1,2,3,4]`
Higher-Order Function - function that takes another function as an argument or returns a function
Expression - combination of variables, functions, operators that produce a value.

---
class: left

## Elixir Building Blocks

```elixir
# chars, strings, integers, floats and booleans

# atoms ->  a constant whose value is its own name
:ok
:error

# Tuples
{:ok, "result"}
{:error, "reason"}

# (Linked) Lists
[1,2,3,4]

# Maps
%{"key" => 1, "key2" => 2}

# Anonymous Functions
fn (a, b) -> a + b end
&(&1 + &2)

# Modules and Functions
defmodule Numbers do
  def product_of_numbers(list_of_numbers) do
    Enum.reduce(list_of_numbers, fn x, acc ->
      acc * x
    end)
  end
end
```
---
class: left
## How do you know the type of the parameters?

Typical Elixir Function

```elixir
def product_of_numbers(list_of_numbers) do
  Enum.reduce(list_of_numbers, fn x, acc ->
    acc * x
  end)
end
```

---
class: left
## How do you know the type of the parameters?

Typical Elixir Function

```elixir
def product_of_numbers(list_of_numbers) do
  Enum.reduce(list_of_numbers, fn x, acc ->
    acc * x
  end)
end
```
I can provide specifications to give Elixir hints as to what it should expect

```elixir
@spec product_of_numbers(list(number())) :: number()
def product_of_numbers(list_of_numbers) do
  Enum.reduce(list_of_numbers, fn x, acc ->
    acc * x
  end)
end
```

And then run the static analysis tool to find most issues.

???

* Change a parameter in the list of use_fn function to a string and run show that it compiles fine, but throws an error when called.
* Run Dialyzer to show the error

---
class: left

## Composability (and the Pipe operator)

Because Elixir's focus is on transforming data, composability is intuitive and the pipe operator makes it very elegant

Instead of writing like this...

```elixir
odd? = &(rem(&1, 2) != 0) end

Enum.sum(
  Enum.filter(
    Enum.map(1..100_000, &(&1 * 3)),
    odd?
  )
)
```
---
class: left

## Composability (and the Pipe operator)

Because Elixir's focus is on transforming data, composability is intuitive and the pipe operator makes it very elegant

Instead of writing like this...

```elixir
odd? = &(rem(&1, 2) != 0) end

Enum.sum(
  Enum.filter(
    Enum.map(1..100_000, &(&1 * 3)),
    odd?
  )
)
```

You can do:

```elixir
odd? = &(rem(&1, 2) != 0) end

1..100_000
|> Enum.map(&(&1 * 3))
|> Enum.filter(odd?)
|> Enum.sum

```


---
class: left

## Pattern Matching

case expressions
```elixir
added =
  case some_array() do
    [a, b] -> {:ok, a + b}
    [] -> :empty
    _ -> :error
  end
```
function arguments
```elixir
print_num(added)

def print_num({:ok, num}) do
  IO.puts("The elements equal #{num}")
end

def print_num(:error) do
  IO.puts("Someting went very wrong");
end

def print_num(:empty) do
  IO.puts("Can not add elements of an empty list");
end

```
---
class: left

## Built in documentation with testing

```elixir
defmodule Numbers do
  @moduledoc "Functions that help you deal with numbers"

  @doc """
    Takes a list of numbers and returns the product of them

    ## Examples

        iex> product_of_numbers([1,2,3])
        6
  """
  def product_of_numbers(list_of_numbers) do
    Enum.reduce(list_of_numbers, fn x, acc ->
      acc * x
    end)
  end
end
```

[Documentation Example](https://hexdocs.pm/elixir/Enum.html)


---
class: left

## The REPL

* can be used in context of an application
* can be used for debugging purposes and tracing.
* can recompile modules on the fly
* includes documentation and help

###Example

???
`break! ElixirForAlchemists.product_of_numbers/1`
`continue`
---
class: left

## Elixir Concurrency

.left-column-half[
Uses the 'Actor' model.

Processes are isolated from each other, run concurrent to one another and communicate via message passing.

Processes are very lightweight and it's not uncommon for Elixir programs to run tens or hundreds of thousands of processes at any given time.

The BEAM manages scheduling of all these process among all available CPU's
]

.right-column-half[
  ![Actor_Model](assets/actors.png)
]

???

`pid = spawn(Processes, :listen, [])`
`send(pid, {:ok, "hello", self})`


---
class: left

## Distributed Elixir

Building upon the isolated process (Actor) model, the BEAM (Erlang Virtual Machine) can easily run processes on seperate machines passing messages around as if they are on the same machine.

#### Example

???

iex --sname one@localhost -S mix

iex --sname two@localhost -S mix

pid = Node.spawn(:one@localhost, Processes, :hello, [])

message_pid = Node.spawn(:two@localhost, Processes, :message, [])

send(pid, {:ok, "hello", message_pid})

---
class: left

## Thanks

#### Resources for learning Elixir
* [Elixir Lang Website Guide](https://elixir-lang.org/getting-started/introduction.html)
* [Elixir School](https://elixirschool.com/en/)
* [Elixir Casts](https://elixircasts.io/)
* [Exercism](https://exercism.io/tracks/elixir)
* [Functional vs OO](https://chelseatroy.com/2021/02/22/functional-vs-oo-the-debate-that-imprecise-language-destroyed/)
* Come talk to me!

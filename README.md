# DeltaCrdt

DeltaCrdt implements some Delta CRDTs in Elixir.

CRDTs currently offered include:
- Add Wins Last Write Wins Map
- Add Wins Set
- Observed Remove Map

Please open an issue or a pull request if you'd like to see any additional Delta CRDTs included.

The following papers have been partially implemented in this library:
- [`Delta State Replicated Data Types – Almeida et al. 2016`](https://arxiv.org/pdf/1603.01529.pdf)
- [`Efficient Synchronization of State-based CRDTs – Enes et al. 2018`](https://arxiv.org/pdf/1803.02750.pdf)

## Usage

Documentation can be found on [hexdocs.pm](https://hexdocs.pm/delta_crdt).

Here's a short example to illustrate adding an entry to a map:

```elixir
alias DeltaCrdt.{CausalCrdt, AWLWWMap}

# start 2 GenServers to wrap a CRDT.
{:ok, crdt} = CausalCrdt.start_link(AWLWWMap)
{:ok, crdt2} = CausalCrdt.start_link(AWLWWMap)

# make them aware of each other
send(crdt, {:add_neighbours, [crdt2]})
send(crdt2, {:add_neighbours, [crdt]})

# do an operation on the CRDT
GenServer.cast(crdt, {:operation, {:add, ["CRDT", "is magic"]}})

# force sending intervals to neighbours
send(crdt, :ship_interval_or_state_to_all)

# wait a few ms to give it time to replicate
Process.sleep(50)

# read the CRDT
CausalCrdt.read(crdt2)
#=> %{"CRDT" => "is magic"}
```

## TODOs

- implement join decomposition to further reduce back-propagation.

## Installation

The package can be installed by adding `delta_crdt` to your list of dependencies in `mix.exs`:

```elixir
def deps do
  [
    {:delta_crdt, "~> 0.1.10"}
  ]
end
```

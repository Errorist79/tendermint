# loadtime

This directory contains the `loadtime` tools, a set of tools for generating
transaction load against Tendermint and measuring their resulting latency.
`loadtime` generates transactions that contain the timestamp of when they were
generated as well as additional metadata to track the variables used when
generating the load.


## Building the tool set

The `Makefile` contains a target for building the `loadtime` tools.

The following command will build the tool and place the resulting binaries in `./build/`.

```bash
make build
```

## `load`

The `load` binary is built when `make build` is invoked. The `load` tool generates
transactions and broadcasts them to Tendermint.

`load` leverages the [tm-load-test](https://github.com/informalsystems/tm-load-test)
framework. As a result, all flags and options specified on the `tm-load-test` apply to
`load`.

Below is a basic invocation for generating load against a Tendermint websocket running
on `localhost:25567`

```bash
./build/load \
    -c 1 -T 10 -r 1000 -s 1024 \
    --broadcast-tx-method sync \
    --endpoints ws://localhost:26657/websocket
```

## `report`

The `report` binary is built when `make build` is invoked. The `report` tool
reads all of the blocks from the specified blockstore database and calculates
transaction latency metrics. `report` reads transactions generated by `load`
and uses the difference between the timestamp contained in the transaction and
the timestamp of the block the transaction was executed in to determine transaction latency.
`report` outputs a set of metrics calculated on the list of latencies, including
minimum, maximum, and average latency as well as the standard deviation.

Below is a basic invocation of the report tool with a data directory under `/home/test/.tendermint/data/`
where the data was saved in a `goleveldb` database.


```bash
./build/report --database-type goleveldb --data-dir ~/.tendermint/data
```


The `report` tool also supports outputting the raw data as `csv`. This can be
useful if you want to use a more powerful tool to aggregate and analyze the data.

Below is an invocation of the report tool that outputs the data to a `csv` file
in `out.csv`

```bash
./build/report --database-type goleveldb --data-dir ~/.tendermint/data --csv out.csv
```

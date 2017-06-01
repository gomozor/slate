---
title: RPC Reference

language_tabs:
  - shell
  - go

toc_footers:
  - <a href='https://tendermint.com/'>Tendermint</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

search: true
---

# Introduction

Tendermint supports the following RPC protocols:

* URI over HTTP
* JSONRPC over HTTP
* JSONRPC over websockets

Tendermint RPC is build using [our own RPC library](<a href="https://github.com/tendermint/tendermint/tree/master/rpc/lib">https://github.com/tendermint/tendermint/tree/master/rpc/lib</a>). Documentation and tests for that library could be found at `tendermint/rpc/lib` directory.

## Configuration

Set the `laddr` config parameter under `[rpc]` table in the `$TMHOME/config.toml` file or the `--rpc.laddr` command-line flag to the desired protocol://host:port setting.Default: `tcp://0.0.0.0:46657`.

## Arguments

Arguments which expect strings or byte arrays may be passed as quoted strings, like `"abc"` or as `0x`-prefixed strings, like `0x616263`.

## URI/HTTP

```bash
curl 'localhost:46657/broadcast_tx_sync?tx="abc"'
```

> Response:

```json
{
  "error": "",
  "result": {
    "hash": "2B8EC32BA2579B3B8606E42C06DE2F7AFA2556EF",
    "log": "",
    "data": "",
    "code": 0
  },
  "id": "",
  "jsonrpc": "2.0"
}
```

The first entry in the result-array (`96`) is the method this response correlates with. `96` refers to "ResultTypeBroadcastTx", see [responses.go](<a href="https://github.com/tendermint/tendermint/blob/master/rpc/core/types/responses.go">https://github.com/tendermint/tendermint/blob/master/rpc/core/types/responses.go</a>) for a complete overview.

## JSONRPC/HTTP

JSONRPC requests can be POST'd to the root RPC endpoint via HTTP (e.g. `<a href="http://localhost:46657/">http://localhost:46657/</a>`).

```json
{
  "method": "broadcast_tx_sync",
  "jsonrpc": "2.0",
  "params": [ "abc" ],
  "id": "dontcare"
}
```

## JSONRPC/websockets

JSONRPC requests can be made via websocket. The websocket endpoint is at `/websocket`, e.g. `localhost:46657/websocket`.  Asynchronous RPC functions like event `subscribe`and `unsubscribe` are only available via websockets.

## More Examples

See the various bash tests using curl in `test/`, and examples using the `Go` API in `rpc/client/`.

## Get the list

An HTTP Get request to the root RPC endpoint shows a list of available endpoints.

```bash
curl 'localhost:46657'
```

> Response:

```plain
Available endpoints:
/abci_info
/dump_consensus_state
/genesis
/net_info
/num_unconfirmed_txs
/status
/unconfirmed_txs
/unsafe_flush_mempool
/unsafe_stop_cpu_profiler
/validators

Endpoints that require arguments:
/abci_query?path=_&data=_&prove=_
/block?height=_
/blockchain?minHeight=_&maxHeight=_
/broadcast_tx_async?tx=_
/broadcast_tx_commit?tx=_
/broadcast_tx_sync?tx=_
/commit?height=_
/dial_seeds?seeds=_
/subscribe?event=_
/tx?hash=_&prove=_
/unsafe_start_cpu_profiler?filename=_
/unsafe_write_heap_profile?filename=_
/unsubscribe?event=_
```

# Endpoints

## [ABCIInfo](https://github.com/tendermint/tendermint/tree/master/rpc/core/abci.go?s=2212:2259#L78)
Get some info about the application.

```shell
curl 'localhost:46657/abci_info'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:46657", "/websocket")
info, err := client.ABCIInfo()
```

> The above command returns JSON structured like this:

```json
{
  "error": "",
  "result": {
    "response": {
      "data": "{\"size\":3}"
    }
  },
  "id": "",
  "jsonrpc": "2.0"
}
```

## [ABCIQuery](https://github.com/tendermint/tendermint/tree/master/rpc/core/abci.go?s=1394:1483#L38)
Query the application for some information.

```shell
curl 'localhost:46657/abci_query?path=""&data="abcd"&prove=true'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:46657", "/websocket")
result, err := client.ABCIQuery("", "abcd", true)
```

> The above command returns JSON structured like this:

```json
{
  "error": "",
  "result": {
    "response": {
      "log": "exists",
      "height": 0,
      "proof": "010114FED0DAD959F36091AD761C922ABA3CBF1D8349990101020103011406AA2262E2F448242DF2C2607C3CDC705313EE3B0001149D16177BC71E445476174622EA559715C293740C",
      "value": "61626364",
      "key": "61626364",
      "index": -1,
      "code": 0
    }
  },
  "id": "",
  "jsonrpc": "2.0"
}
```

### Query Parameters

| Parameter | Type   | Default | Required | Description                           |
|-----------+--------+---------+----------+---------------------------------------|
| path      | string | false   | false    | Path to the data ("/a/b/c")           |
| data      | []byte | false   | true     | Data                                  |
| prove     | bool   | false   | false    | Include a proof of the data inclusion |

## [Block](https://github.com/tendermint/tendermint/tree/master/rpc/core/blocks.go?s=5191:5242#L176)
Get block at a given height.

```shell
curl 'localhost:46657/block?height=10'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:46657", "/websocket")
info, err := client.Block(10)
```

> The above command returns JSON structured like this:

```json
{
  "error": "",
  "result": {
    "block": {
      "last_commit": {
        "precommits": [
        {
          "signature": {
            "data": "12C0D8893B8A38224488DC1DE6270DF76BB1A5E9DB1C68577706A6A97C6EC34FFD12339183D5CA8BC2F46148773823DE905B7F6F5862FD564038BB7AE03BF50D",
            "type": "ed25519"
          },
          "block_id": {
            "parts": {
              "hash": "3C78F00658E06744A88F24FF97A0A5011139F34A",
              "total": 1
            },
            "hash": "F70588DAB36BDA5A953D548A16F7D48C6C2DFD78"
          },
          "type": 2,
          "round": 0,
          "height": 9,
          "validator_index": 0,
          "validator_address": "E89A51D60F68385E09E716D353373B11F8FACD62"
        }
        ],
        "blockID": {
          "parts": {
            "hash": "3C78F00658E06744A88F24FF97A0A5011139F34A",
            "total": 1
          },
          "hash": "F70588DAB36BDA5A953D548A16F7D48C6C2DFD78"
        }
      },
      "data": {
        "txs": []
      },
      "header": {
        "app_hash": "",
        "chain_id": "test-chain-6UTNIN",
        "height": 10,
        "time": "2017-05-29T15:05:53.877Z",
        "num_txs": 0,
        "last_block_id": {
          "parts": {
            "hash": "3C78F00658E06744A88F24FF97A0A5011139F34A",
            "total": 1
          },
          "hash": "F70588DAB36BDA5A953D548A16F7D48C6C2DFD78"
        },
        "last_commit_hash": "F31CC4282E50B3F2A58D763D233D76F26D26CABE",
        "data_hash": "",
        "validators_hash": "9365FC80F234C967BD233F5A3E2AB2F1E4B0E5AA"
      }
    },
    "block_meta": {
      "header": {
        "app_hash": "",
        "chain_id": "test-chain-6UTNIN",
        "height": 10,
        "time": "2017-05-29T15:05:53.877Z",
        "num_txs": 0,
        "last_block_id": {
          "parts": {
            "hash": "3C78F00658E06744A88F24FF97A0A5011139F34A",
            "total": 1
          },
          "hash": "F70588DAB36BDA5A953D548A16F7D48C6C2DFD78"
        },
        "last_commit_hash": "F31CC4282E50B3F2A58D763D233D76F26D26CABE",
        "data_hash": "",
        "validators_hash": "9365FC80F234C967BD233F5A3E2AB2F1E4B0E5AA"
      },
      "block_id": {
        "parts": {
          "hash": "277A4DBEF91483A18B85F2F5677ABF9694DFA40F",
          "total": 1
        },
        "hash": "96B1D2F2D201BA4BC383EB8224139DB1294944E5"
      }
    }
  },
  "id": "",
  "jsonrpc": "2.0"
}
```

## [BlockchainInfo](https://github.com/tendermint/tendermint/tree/master/rpc/core/blocks.go?s=1511:1594#L54)
Get block headers for minHeight <= height <= maxHeight.

```shell
curl 'localhost:46657/blockchain?minHeight=10&maxHeight=10'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:46657", "/websocket")
info, err := client.BlockchainInfo(10, 10)
```

> The above command returns JSON structured like this:

```json
{
  "error": "",
  "result": {
    "block_metas": [
    {
      "header": {
        "app_hash": "",
        "chain_id": "test-chain-6UTNIN",
        "height": 10,
        "time": "2017-05-29T15:05:53.877Z",
        "num_txs": 0,
        "last_block_id": {
          "parts": {
            "hash": "3C78F00658E06744A88F24FF97A0A5011139F34A",
            "total": 1
          },
          "hash": "F70588DAB36BDA5A953D548A16F7D48C6C2DFD78"
        },
        "last_commit_hash": "F31CC4282E50B3F2A58D763D233D76F26D26CABE",
        "data_hash": "",
        "validators_hash": "9365FC80F234C967BD233F5A3E2AB2F1E4B0E5AA"
      },
      "block_id": {
        "parts": {
          "hash": "277A4DBEF91483A18B85F2F5677ABF9694DFA40F",
          "total": 1
        },
        "hash": "96B1D2F2D201BA4BC383EB8224139DB1294944E5"
      }
    }
    ],
    "last_height": 5493
  },
  "id": "",
  "jsonrpc": "2.0"
}
```

<aside class="notice">Returns at most 20 items.</aside>

## [BroadcastTxAsync](https://github.com/tendermint/tendermint/tree/master/rpc/core/mempool.go?s=1140:1209#L38)
Returns right away, with no response

```shell
curl 'localhost:46657/broadcast_tx_async?tx="123"'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:46657", "/websocket")
result, err := client.BroadcastTxAsync("123")
```

> The above command returns JSON structured like this:

```json
{
  "error": "",
  "result": {
    "hash": "E39AAB7A537ABAA237831742DCE1117F187C3C52",
    "log": "",
    "data": "",
    "code": 0
  },
  "id": "",
  "jsonrpc": "2.0"
}
```

### Query Parameters

| Parameter | Type | Default | Required | Description     |
|-----------+------+---------+----------+-----------------|
| tx        | Tx   | nil     | true     | The transaction |

## [BroadcastTxCommit](https://github.com/tendermint/tendermint/tree/master/rpc/core/mempool.go?s=3642:3718#L139)
CONTRACT: only returns error if mempool.BroadcastTx errs (ie. problem with the app)
or if we timeout waiting for tx to commit.
If CheckTx or DeliverTx fail, no error will be returned, but the returned result
will contain a non-OK ABCI code.

```shell
curl 'localhost:46657/broadcast_tx_commit?tx="789"'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:46657", "/websocket")
result, err := client.BroadcastTxCommit("789")
```

> The above command returns JSON structured like this:

```json
{
  "error": "",
  "result": {
    "height": 26682,
    "hash": "75CA0F856A4DA078FC4911580360E70CEFB2EBEE",
    "deliver_tx": {
      "log": "",
      "data": "",
      "code": 0
    },
    "check_tx": {
      "log": "",
      "data": "",
      "code": 0
    }
  },
  "id": "",
  "jsonrpc": "2.0"
}
```

### Query Parameters

| Parameter | Type | Default | Required | Description     |
|-----------+------+---------+----------+-----------------|
| tx        | Tx   | nil     | true     | The transaction |

## [BroadcastTxSync](https://github.com/tendermint/tendermint/tree/master/rpc/core/mempool.go?s=2133:2201#L78)
Returns with the response from CheckTx.

```shell
curl 'localhost:46657/broadcast_tx_sync?tx="456"'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:46657", "/websocket")
result, err := client.BroadcastTxSync("456")
```

> The above command returns JSON structured like this:

```json
{
  "jsonrpc": "2.0",
  "id": "",
  "result": {
    "code": 0,
    "data": "",
    "log": "",
    "hash": "0D33F2F03A5234F38706E43004489E061AC40A2E"
  },
  "error": ""
}
```

### Query Parameters

| Parameter | Type | Default | Required | Description     |
|-----------+------+---------+----------+-----------------|
| tx        | Tx   | nil     | true     | The transaction |

## [Commit](https://github.com/tendermint/tendermint/tree/master/rpc/core/blocks.go?s=7571:7624#L258)
Get block commit at a given height.

```shell
curl 'localhost:46657/commit?height=11'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:46657", "/websocket")
info, err := client.Commit(11)
```

> The above command returns JSON structured like this:

```json
{
  "error": "",
  "result": {
    "canonical": true,
    "commit": {
      "precommits": [
      {
        "signature": {
          "data": "00970429FEC652E9E21D106A90AE8C5413759A7488775CEF4A3F44DC46C7F9D941070E4FBE9ED54DF247FA3983359A0C3A238D61DE55C75C9116D72ABC9CF50F",
          "type": "ed25519"
        },
        "block_id": {
          "parts": {
            "hash": "9E37CBF266BC044A779E09D81C456E653B89E006",
            "total": 1
          },
          "hash": "CC6E861E31CA4334E9888381B4A9137D1458AB6A"
        },
        "type": 2,
        "round": 0,
        "height": 11,
        "validator_index": 0,
        "validator_address": "E89A51D60F68385E09E716D353373B11F8FACD62"
      }
      ],
      "blockID": {
        "parts": {
          "hash": "9E37CBF266BC044A779E09D81C456E653B89E006",
          "total": 1
        },
        "hash": "CC6E861E31CA4334E9888381B4A9137D1458AB6A"
      }
    },
    "header": {
      "app_hash": "",
      "chain_id": "test-chain-6UTNIN",
      "height": 11,
      "time": "2017-05-29T15:05:54.893Z",
      "num_txs": 0,
      "last_block_id": {
        "parts": {
          "hash": "277A4DBEF91483A18B85F2F5677ABF9694DFA40F",
          "total": 1
        },
        "hash": "96B1D2F2D201BA4BC383EB8224139DB1294944E5"
      },
      "last_commit_hash": "3CE0C9727CE524BA9CB7C91E28F08E2B94001087",
      "data_hash": "",
      "validators_hash": "9365FC80F234C967BD233F5A3E2AB2F1E4B0E5AA"
    }
  },
  "id": "",
  "jsonrpc": "2.0"
}
```

## [DumpConsensusState](https://github.com/tendermint/tendermint/tree/master/rpc/core/consensus.go?s=2993:3060#L64)
Dump consensus state.

```shell
curl 'localhost:46657/dump_consensus_state'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:46657", "/websocket")
state, err := client.DumpConsensusState()
```

> The above command returns JSON structured like this:

```json
{
  "error": "",
  "result": {
    "peer_round_states": [],
    "round_state": "RoundState{\n  H:3537 R:0 S:RoundStepNewHeight\n  StartTime:     2017-05-31 12:32:31.178653883 +0000 UTC\n  CommitTime:    2017-05-31 12:32:30.178653883 +0000 UTC\n  Validators:    ValidatorSet{\n      Proposer: Validator{E89A51D60F68385E09E716D353373B11F8FACD62 {PubKeyEd25519{68DFDA7E50F82946E7E8546BED37944A422CD1B831E70DF66BA3B8430593944D}} VP:10 A:0}\n      Validators:\n        Validator{E89A51D60F68385E09E716D353373B11F8FACD62 {PubKeyEd25519{68DFDA7E50F82946E7E8546BED37944A422CD1B831E70DF66BA3B8430593944D}} VP:10 A:0}\n    }\n  Proposal:      <nil>\n  ProposalBlock: nil-PartSet nil-Block\n  LockedRound:   0\n  LockedBlock:   nil-PartSet nil-Block\n  Votes:         HeightVoteSet{H:3537 R:0~0\n      VoteSet{H:3537 R:0 T:1 +2/3:<nil> BA{1:_} map[]}\n      VoteSet{H:3537 R:0 T:2 +2/3:<nil> BA{1:_} map[]}\n    }\n  LastCommit: VoteSet{H:3536 R:0 T:2 +2/3:B7F988FBCDC68F9320E346EECAA76E32F6054654:1:673BE7C01F74 BA{1:X} map[]}\n  LastValidators:    ValidatorSet{\n      Proposer: Validator{E89A51D60F68385E09E716D353373B11F8FACD62 {PubKeyEd25519{68DFDA7E50F82946E7E8546BED37944A422CD1B831E70DF66BA3B8430593944D}} VP:10 A:0}\n      Validators:\n        Validator{E89A51D60F68385E09E716D353373B11F8FACD62 {PubKeyEd25519{68DFDA7E50F82946E7E8546BED37944A422CD1B831E70DF66BA3B8430593944D}} VP:10 A:0}\n    }\n}"
  },
  "id": "",
  "jsonrpc": "2.0"
}
```

## [Genesis](https://github.com/tendermint/tendermint/tree/master/rpc/core/net.go?s=2354:2399#L98)
Get genesis file.

```shell
curl 'localhost:46657/genesis'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:46657", "/websocket")
genesis, err := client.Genesis()
```

> The above command returns JSON structured like this:

```json
{
  "error": "",
  "result": {
    "genesis": {
      "app_hash": "",
      "validators": [
      {
        "name": "",
        "amount": 10,
        "pub_key": {
          "data": "68DFDA7E50F82946E7E8546BED37944A422CD1B831E70DF66BA3B8430593944D",
          "type": "ed25519"
        }
      }
      ],
      "chain_id": "test-chain-6UTNIN",
      "genesis_time": "2017-05-29T15:05:41.671Z"
    }
  },
  "id": "",
  "jsonrpc": "2.0"
}
```

## [NetInfo](https://github.com/tendermint/tendermint/tree/master/rpc/core/net.go?s=558:603#L26)
Get network info.

```shell
curl 'localhost:46657/net_info'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:46657", "/websocket")
info, err := client.NetInfo()
```

> The above command returns JSON structured like this:

```json
{
  "error": "",
  "result": {
    "peers": [],
    "listeners": [
      "Listener(@10.0.2.15:46656)"
    ],
    "listening": true
  },
  "id": "",
  "jsonrpc": "2.0"
}
```

## [NumUnconfirmedTxs](https://github.com/tendermint/tendermint/tree/master/rpc/core/mempool.go?s=6525:6587#L250)
Get number of unconfirmed transactions.

```shell
curl 'localhost:46657/num_unconfirmed_txs'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:46657", "/websocket")
result, err := client.UnconfirmedTxs()
```

> The above command returns JSON structured like this:

```json
{
  "error": "",
  "result": {
    "txs": null,
    "n_txs": 0
  },
  "id": "",
  "jsonrpc": "2.0"
}
```

## [Status](https://github.com/tendermint/tendermint/tree/master/rpc/core/status.go?s=1544:1587#L46)
Get Tendermint status including node info, pubkey, latest block
hash, app hash, block height and time.

```shell
curl 'localhost:46657/status'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:46657", "/websocket")
result, err := client.Status()
```

> The above command returns JSON structured like this:

```json
{
  "error": "",
  "result": {
    "latest_block_time": 1.49631773695e+18,
    "latest_block_height": 22924,
    "latest_app_hash": "9D16177BC71E445476174622EA559715C293740C",
    "latest_block_hash": "75B36EEF96C277A592D8B14867098C58F68BB180",
    "pub_key": {
      "data": "68DFDA7E50F82946E7E8546BED37944A422CD1B831E70DF66BA3B8430593944D",
      "type": "ed25519"
    },
    "node_info": {
      "other": [
        "wire_version=0.6.2",
      "p2p_version=0.5.0",
      "consensus_version=v1/0.2.2",
      "rpc_version=0.7.0/3",
      "tx_index=on",
      "rpc_addr=tcp://0.0.0.0:46657"
      ],
      "version": "0.10.0-rc1-aa22bd84",
      "listen_addr": "10.0.2.15:46656",
      "remote_addr": "",
      "network": "test-chain-6UTNIN",
      "moniker": "anonymous",
      "pub_key": "659B9E54DD6EF9FEF28FAD40629AF0E4BD3C2563BB037132B054A176E00F1D94"
    }
  },
  "id": "",
  "jsonrpc": "2.0"
}
```

## [Subscribe](https://github.com/tendermint/tendermint/tree/master/rpc/core/events.go?s=853:943#L26)
Subscribe for events via WebSocket.

```go
import 'github.com/tendermint/tendermint/types'

client := client.NewHTTP("tcp://0.0.0.0:46657", "/websocket")
result, err := client.AddListenerForEvent(types.EventStringNewBlock())
```

> The above command returns JSON structured like this:

```json
{
  "error": "",
  "result": {},
  "id": "",
  "jsonrpc": "2.0"
}
```

### Query Parameters

| Parameter | Type   | Default | Required | Description |
|-----------+--------+---------+----------+-------------|
| event     | string | ""      | true     | Event name  |

<aside class="notice">WebSocket only</aside>

## [Tx](https://github.com/tendermint/tendermint/tree/master/rpc/core/tx.go?s=1894:1952#L59)
Tx allows you to query the transaction results. `nil` could mean the
transaction is in the mempool, invalidated, or was not send in the first
place.

```shell
curl "localhost:46657/tx?hash=0x2B8EC32BA2579B3B8606E42C06DE2F7AFA2556EF"
```

```go
client := client.NewHTTP("tcp://0.0.0.0:46657", "/websocket")
tx, err := client.Tx([]byte("2B8EC32BA2579B3B8606E42C06DE2F7AFA2556EF"), true)
```

> The above command returns JSON structured like this:

```json
{
  "error": "",
  "result": {
    "proof": {
      "Proof": {
        "aunts": []
      },
      "Data": "YWJjZA==",
      "RootHash": "2B8EC32BA2579B3B8606E42C06DE2F7AFA2556EF",
      "Total": 1,
      "Index": 0
    },
    "tx": "YWJjZA==",
    "tx_result": {
      "log": "",
      "data": "",
      "code": 0
    },
    "index": 0,
    "height": 52
  },
  "id": "",
  "jsonrpc": "2.0"
}
```

Returns a transaction matching the given transaction hash.

### Query Parameters

| Parameter | Type   | Default | Required | Description                |
|-----------+--------+---------+----------+-----------------------------------------------------------|
| hash      | []byte | nil     | true     | The transaction hash                |
| prove     | bool   | false   | false    | Include a proof of the transaction inclusion in the block |

### Returns

- `proof`: the `types.TxProof` object
- `tx`: `[]byte` - the transaction
- `tx_result`: the `abci.Result` object
- `index`: `int` - index of the transaction
- `height`: `int` - height of the block where this transaction was in

## [UnconfirmedTxs](https://github.com/tendermint/tendermint/tree/master/rpc/core/mempool.go?s=5938:5997#L222)
Get unconfirmed transactions including their number.

```shell
curl 'localhost:46657/unconfirmed_txs'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:46657", "/websocket")
result, err := client.UnconfirmedTxs()
```

> The above command returns JSON structured like this:

```json
{
  "error": "",
  "result": {
    "txs": [],
    "n_txs": 0
  },
  "id": "",
  "jsonrpc": "2.0"
}
```

## [UnsafeDialSeeds](https://github.com/tendermint/tendermint/tree/master/rpc/core/net.go?s=1129:1198#L47)
## [Unsubscribe](https://github.com/tendermint/tendermint/tree/master/rpc/core/events.go?s=2132:2226#L64)
Unsubscribe from events via WebSocket.

```go
import 'github.com/tendermint/tendermint/types'

client := client.NewHTTP("tcp://0.0.0.0:46657", "/websocket")
result, err := client.RemoveListenerForEvent(types.EventStringNewBlock())
```

> The above command returns JSON structured like this:

```json
{
  "error": "",
  "result": {},
  "id": "",
  "jsonrpc": "2.0"
}
```

### Query Parameters

| Parameter | Type   | Default | Required | Description |
|-----------+--------+---------+----------+-------------|
| event     | string | ""      | true     | Event name  |

<aside class="notice">WebSocket only</aside>

## [Validators](https://github.com/tendermint/tendermint/tree/master/rpc/core/consensus.go?s=1056:1107#L35)
Get current validators set along with a block height.

```shell
curl 'localhost:46657/validators'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:46657", "/websocket")
state, err := client.Validators()
```

> The above command returns JSON structured like this:


```json
{
  "error": "",
  "result": {
    "validators": [
    {
      "accum": 0,
      "voting_power": 10,
      "pub_key": {
        "data": "68DFDA7E50F82946E7E8546BED37944A422CD1B831E70DF66BA3B8430593944D",
        "type": "ed25519"
      },
      "address": "E89A51D60F68385E09E716D353373B11F8FACD62"
    }
    ],
    "block_height": 5241
  },
  "id": "",
  "jsonrpc": "2.0"
}
```

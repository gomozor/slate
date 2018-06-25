---
title: RPC Reference

language_tabs:
  - shell
  - go

toc_footers:
  - <a href='https://tendermint.com/'>Tendermint</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

search: true
---

# Introduction

Tendermint supports the following RPC protocols:

* URI over HTTP
* JSONRPC over HTTP
* JSONRPC over websockets

Tendermint RPC is built using [our own RPC library](<a href="https://github.com/tendermint/tendermint/tree/master/rpc/lib">https://github.com/tendermint/tendermint/tree/master/rpc/lib</a>) which contains its own set of documentation and tests.

## Configuration

Set the `laddr` config parameter under `[rpc]` table in the `$TMHOME/config/config.toml` file or the `--rpc.laddr` command-line flag to the desired protocol://host:port setting.  Default: `tcp://0.0.0.0:26657`.

## Arguments

Arguments which expect strings or byte arrays may be passed as quoted strings, like `"abc"` or as `0x`-prefixed strings, like `0x616263`.

## URI/HTTP

```bash
curl 'localhost:26657/broadcast_tx_sync?tx="abc"'
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

JSONRPC requests can be POST'd to the root RPC endpoint via HTTP (e.g. `<a href="http://localhost:26657/">http://localhost:26657/</a>`).

```json
{


	"method": "broadcast_tx_sync",
	"jsonrpc": "2.0",
	"params": [ "abc" ],
	"id": "dontcare"

}
```

## JSONRPC/websockets

JSONRPC requests can be made via websocket. The websocket endpoint is at `/websocket`, e.g. `localhost:26657/websocket`.  Asynchronous RPC functions like event `subscribe` and `unsubscribe` are only available via websockets.

## More Examples

See the various bash tests using curl in `test/`, and examples using the `Go` API in `rpc/client/`.

## Get the list

An HTTP Get request to the root RPC endpoint shows a list of available endpoints.

```bash
curl 'localhost:26657'
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
/health
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
/dial_persistent_peers?persistent_peers=_
/subscribe?event=_
/tx?hash=_&prove=_
/unsafe_start_cpu_profiler?filename=_
/unsafe_write_heap_profile?filename=_
/unsubscribe?event=_
```

# Endpoints




## [ABCIInfo](https://github.com/tendermint/tendermint/tree/master/rpc/core/abci.go?s=2427:2474#L89)
Get some info about the application.

```shell
curl 'localhost:26657/abci_info'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:26657", "/websocket")
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

## [ABCIQuery](https://github.com/tendermint/tendermint/tree/master/rpc/core/abci.go?s=1581:1688#L50)
Query the application for some information.

```shell
curl 'localhost:26657/abci_query?path=""&data="abcd"&trusted=false'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:26657", "/websocket")
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

| Parameter | Type   | Default | Required | Description                                    |
|-----------+--------+---------+----------+------------------------------------------------|
| path      | string | false   | false    | Path to the data ("/a/b/c")                    |
| data      | []byte | false   | true     | Data                                           |
| height    | int64 | 0       | false    | Height (0 means latest)                        |
| trusted   | bool   | false   | false    | Does not include a proof of the data inclusion |

## [Block](https://github.com/tendermint/tendermint/tree/master/rpc/core/blocks.go?s=5535:5592#L196)
Get block at a given height.
If no height is provided, it will fetch the latest block.

```shell
curl 'localhost:26657/block?height=10'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:26657", "/websocket")
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

## [BlockResults](https://github.com/tendermint/tendermint/tree/master/rpc/core/blocks.go?s=9260:9331#L332)
BlockResults gets ABCIResults at a given height.
If no height is provided, it will fetch results for the latest block.

Results are for the height of the block containing the txs.
Thus response.results[5] is the results of executing getBlock(h).Txs[5]

```shell
curl 'localhost:26657/block_results?height=10'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:26657", "/websocket")
info, err := client.BlockResults(10)
```

> The above command returns JSON structured like this:

```json
{


	"height": 10,
	"results": [
	 {
	  "code": 0,
	  "data": "CAFE00F00D"
	 },
	 {
	  "code": 102,
	  "data": ""
	 }
	]

}
```

## [BlockchainInfo](https://github.com/tendermint/tendermint/tree/master/rpc/core/blocks.go?s=1625:1710#L66)
Get block headers for minHeight <= height <= maxHeight.
Block headers are returned in descending order (highest first).

```shell
curl 'localhost:26657/blockchain?minHeight=10&maxHeight=10'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:26657", "/websocket")
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

## [BroadcastTxAsync](https://github.com/tendermint/tendermint/tree/master/rpc/core/mempool.go?s=1177:1246#L51)
Returns right away, with no response

```shell
curl 'localhost:26657/broadcast_tx_async?tx="123"'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:26657", "/websocket")
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

## [BroadcastTxCommit](https://github.com/tendermint/tendermint/tree/master/rpc/core/mempool.go?s=3679:3755#L152)
CONTRACT: only returns error if mempool.BroadcastTx errs (ie. problem with the app)
or if we timeout waiting for tx to commit.
If CheckTx or DeliverTx fail, no error will be returned, but the returned result
will contain a non-OK ABCI code.

```shell
curl 'localhost:26657/broadcast_tx_commit?tx="789"'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:26657", "/websocket")
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

## [BroadcastTxSync](https://github.com/tendermint/tendermint/tree/master/rpc/core/mempool.go?s=2170:2238#L91)
Returns with the response from CheckTx.

```shell
curl 'localhost:26657/broadcast_tx_sync?tx="456"'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:26657", "/websocket")
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

## [Commit](https://github.com/tendermint/tendermint/tree/master/rpc/core/blocks.go?s=7921:7980#L278)
Get block commit at a given height.
If no height is provided, it will fetch the commit for the latest block.

```shell
curl 'localhost:26657/commit?height=11'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:26657", "/websocket")
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

## [ConsensusState](https://github.com/tendermint/tendermint/tree/master/rpc/core/consensus.go?s=7002:7061#L257)
ConsensusState returns a concise summary of the consensus state.
UNSTABLE

```shell
curl 'localhost:26657/consensus_state'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:26657", "/websocket")
state, err := client.ConsensusState()
```

The above command returns JSON structured like this:

```json
{


	"jsonrpc": "2.0",
	"id": "",
	"result": {
	  "round_state": {
	    "height/round/step": "9336/0/1",
	    "start_time": "2018-05-14T10:25:45.72595357-04:00",
	    "proposal_block_hash": "",
	    "locked_block_hash": "",
	    "valid_block_hash": "",
	    "height_vote_set": [
	      {
	        "round": 0,
	        "prevotes": [
	          "nil-Vote"
	        ],
	        "prevotes_bit_array": "BA{1:_} 0/10 = 0.00",
	        "precommits": [
	          "nil-Vote"
	        ],
	        "precommits_bit_array": "BA{1:_} 0/10 = 0.00"
	      }
	    ]
	  }
	}

}
```

## [DumpConsensusState](https://github.com/tendermint/tendermint/tree/master/rpc/core/consensus.go?s=5259:5326#L190)
DumpConsensusState dumps consensus state.
UNSTABLE

```shell
curl 'localhost:26657/dump_consensus_state'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:26657", "/websocket")
state, err := client.DumpConsensusState()
```

The above command returns JSON structured like this:

```json
{


	"jsonrpc": "2.0",
	"id": "",
	"result": {
	  "round_state": {
	    "height": 7185,
	    "round": 0,
	    "step": 1,
	    "start_time": "2018-05-12T13:57:28.440293621-07:00",
	    "commit_time": "2018-05-12T13:57:27.440293621-07:00",
	    "validators": {
	      "validators": [
	        {
	          "address": "B5B3D40BE53982AD294EF99FF5A34C0C3E5A3244",
	          "pub_key": {
	            "type": "AC26791624DE60",
	            "value": "SBctdhRBcXtBgdI/8a/alTsUhGXqGs9k5ylV1u5iKHg="
	          },
	          "voting_power": 10,
	          "accum": 0
	        }
	      ],
	      "proposer": {
	        "address": "B5B3D40BE53982AD294EF99FF5A34C0C3E5A3244",
	        "pub_key": {
	          "type": "AC26791624DE60",
	          "value": "SBctdhRBcXtBgdI/8a/alTsUhGXqGs9k5ylV1u5iKHg="
	        },
	        "voting_power": 10,
	        "accum": 0
	      }
	    },
	    "proposal": null,
	    "proposal_block": null,
	    "proposal_block_parts": null,
	    "locked_round": 0,
	    "locked_block": null,
	    "locked_block_parts": null,
	    "valid_round": 0,
	    "valid_block": null,
	    "valid_block_parts": null,
	    "votes": [
	      {
	        "round": 0,
	        "prevotes": "_",
	        "precommits": "_"
	      }
	    ],
	    "commit_round": -1,
	    "last_commit": {
	      "votes": [
	        "Vote{0:B5B3D40BE539 7184/00/2(Precommit) 14F946FA7EF0 /702B1B1A602A.../ @ 2018-05-12T20:57:27.342Z}"
	      ],
	      "votes_bit_array": "x",
	      "peer_maj_23s": {}
	    },
	    "last_validators": {
	      "validators": [
	        {
	          "address": "B5B3D40BE53982AD294EF99FF5A34C0C3E5A3244",
	          "pub_key": {
	            "type": "AC26791624DE60",
	            "value": "SBctdhRBcXtBgdI/8a/alTsUhGXqGs9k5ylV1u5iKHg="
	          },
	          "voting_power": 10,
	          "accum": 0
	        }
	      ],
	      "proposer": {
	        "address": "B5B3D40BE53982AD294EF99FF5A34C0C3E5A3244",
	        "pub_key": {
	          "type": "AC26791624DE60",
	          "value": "SBctdhRBcXtBgdI/8a/alTsUhGXqGs9k5ylV1u5iKHg="
	        },
	        "voting_power": 10,
	        "accum": 0
	      }
	    }
	  },
	  "peers": [
	    {
	      "node_address": "30ad1854af22506383c3f0e57fb3c7f90984c5e8@172.16.63.221:26656",
	      "peer_state": {
	        "round_state": {
	          "height": 7185,
	          "round": 0,
	          "step": 1,
	          "start_time": "2018-05-12T13:57:27.438039872-07:00",
	          "proposal": false,
	          "proposal_block_parts_header": {
	            "total": 0,
	            "hash": ""
	          },
	          "proposal_block_parts": null,
	          "proposal_pol_round": -1,
	          "proposal_pol": "_",
	          "prevotes": "_",
	          "precommits": "_",
	          "last_commit_round": 0,
	          "last_commit": "x",
	          "catchup_commit_round": -1,
	          "catchup_commit": "_"
	        },
	        "stats": {
	          "last_vote_height": 7184,
	          "votes": 255,
	          "last_block_part_height": 7184,
	          "block_parts": 255
	        }
	      }
	    }
	  ]
	}

}
```

## [Genesis](https://github.com/tendermint/tendermint/tree/master/rpc/core/net.go?s=3065:3110#L125)
Get genesis file.

```shell
curl 'localhost:26657/genesis'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:26657", "/websocket")
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
					"power": 10,
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

## [Health](https://github.com/tendermint/tendermint/tree/master/rpc/core/health.go?s=519:562#L29)
Get node health. Returns empty result (200 OK) on success, no response - in
case of an error.

```shell
curl 'localhost:26657/health'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:26657", "/websocket")
result, err := client.Health()
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

## [NetInfo](https://github.com/tendermint/tendermint/tree/master/rpc/core/net.go?s=594:639#L37)
Get network info.

```shell
curl 'localhost:26657/net_info'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:26657", "/websocket")
info, err := client.NetInfo()
```

> The above command returns JSON structured like this:

```json
{


	"error": "",
	"result": {
		"n_peers": 0,
		"peers": [],
		"listeners": [
			"Listener(@10.0.2.15:26656)"
		],
		"listening": true
	},
	"id": "",
	"jsonrpc": "2.0"

}
```

## [NumUnconfirmedTxs](https://github.com/tendermint/tendermint/tree/master/rpc/core/mempool.go?s=7165:7227#L274)
Get number of unconfirmed transactions.

```shell
curl 'localhost:26657/num_unconfirmed_txs'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:26657", "/websocket")
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

## [Status](https://github.com/tendermint/tendermint/tree/master/rpc/core/status.go?s=1719:1762#L66)
Get Tendermint status including node info, pubkey, latest block
hash, app hash, block height and time.

```shell
curl 'localhost:26657/status'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:26657", "/websocket")
result, err := client.Status()
```

> The above command returns JSON structured like this:

```json
{


	"jsonrpc": "2.0",
	"id": "",
	"result": {
	  "node_info": {
	    "id": "562dd7f579f0ecee8c94a11a3c1e378c1876f433",
	    "listen_addr": "192.168.1.2:26656",
	    "network": "test-chain-I6zScH",
	    "version": "0.19.0",
	    "channels": "4020212223303800",
	    "moniker": "Ethans-MacBook-Pro.local",
	    "other": [
	      "amino_version=0.9.8",
	      "p2p_version=0.5.0",
	      "consensus_version=v1/0.2.2",
	      "rpc_version=0.7.0/3",
	      "tx_index=on",
	      "rpc_addr=tcp://0.0.0.0:26657"
	    ]
	  },
	  "sync_info": {
	    "latest_block_hash": "2D4D7055BE685E3CB2410603C92AD37AE557AC59",
	    "latest_app_hash": "0000000000000000",
	    "latest_block_height": 231,
	    "latest_block_time": "2018-04-27T23:18:08.459766485-04:00",
	    "syncing": false
	  },
	  "validator_info": {
	    "address": "5875562FF0FFDECC895C20E32FC14988952E99E7",
	    "pub_key": {
	      "type": "AC26791624DE60",
	      "value": "PpDJRUrLG2RgFqYYjawfn/AcAgacSXpLFrmfYYQnuzE="
	    },
	    "voting_power": 10
	  }
	}

}
```

## [Subscribe](https://github.com/tendermint/tendermint/tree/master/rpc/core/events.go?s=2799:2889#L87)
Subscribe for events via WebSocket.

To tell which events you want, you need to provide a query. query is a
string, which has a form: "condition AND condition ..." (no OR at the
moment). condition has a form: "key operation operand". key is a string with
a restricted set of possible symbols ( \t\n\r\\()"'=>< are not allowed).
operation can be "=", "<", "<=", ">", ">=", "CONTAINS". operand can be a
string (escaped with single quotes), number, date or time.

Examples:


	tm.event = 'NewBlock'								# new blocks
	tm.event = 'CompleteProposal'				# node got a complete proposal
	tm.event = 'Tx' AND tx.hash = 'XYZ' # single transaction
	tm.event = 'Tx' AND tx.height = 5		# all txs of the fifth block
	tx.height = 5												# all txs of the fifth block

Tendermint provides a few predefined keys: tm.event, tx.hash and tx.height.
Note for transactions, you can define additional keys by providing tags with
DeliverTx response.


		DeliverTx{
			Tags: []*KVPair{
				"agent.name": "K",
			}
	  }
	
		tm.event = 'Tx' AND agent.name = 'K'
		tm.event = 'Tx' AND account.created_at >= TIME 2013-05-03T14:45:00Z
		tm.event = 'Tx' AND contract.sign_date = DATE 2017-01-01
		tm.event = 'Tx' AND account.owner CONTAINS 'Igor'

See list of all possible events here
<a href="https://godoc.org/github.com/tendermint/tendermint/types#pkg-constants">https://godoc.org/github.com/tendermint/tendermint/types#pkg-constants</a>

For complete query syntax, check out
<a href="https://godoc.org/github.com/tendermint/tendermint/libs/pubsub/query">https://godoc.org/github.com/tendermint/tendermint/libs/pubsub/query</a>.

```go
import "github.com/tendermint/tendermint/libs/pubsub/query"
import "github.com/tendermint/tendermint/types"

client := client.NewHTTP("tcp://0.0.0.0:26657", "/websocket")
ctx, cancel := context.WithTimeout(context.Background(), timeout)
defer cancel()
query := query.MustParse("tm.event = 'Tx' AND tx.height = 3")
txs := make(chan interface{})
err := client.Subscribe(ctx, "test-client", query, txs)

go func() {


	  for e := range txs {
	    fmt.Println("got ", e.(types.EventDataTx))
		 }

}()
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
| query     | string | ""      | true     | Query       |

<aside class="notice">WebSocket only</aside>

## [Tx](https://github.com/tendermint/tendermint/tree/master/rpc/core/tx.go?s=2104:2162#L74)
Tx allows you to query the transaction results. `nil` could mean the
transaction is in the mempool, invalidated, or was not sent in the first
place.

```shell
curl "localhost:26657/tx?hash=0x2B8EC32BA2579B3B8606E42C06DE2F7AFA2556EF"
```

```go
client := client.NewHTTP("tcp://0.0.0.0:26657", "/websocket")
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
		"height": 52,
		"hash": "2B8EC32BA2579B3B8606E42C06DE2F7AFA2556EF"
	},
	"id": "",
	"jsonrpc": "2.0"

}
```

Returns a transaction matching the given transaction hash.

### Query Parameters

| Parameter | Type   | Default | Required | Description                                               |
|-----------+--------+---------+----------+-----------------------------------------------------------|
| hash      | []byte | nil     | true     | The transaction hash                                      |
| prove     | bool   | false   | false    | Include a proof of the transaction inclusion in the block |

### Returns

- `proof`: the `types.TxProof` object
- `tx`: `[]byte` - the transaction
- `tx_result`: the `abci.Result` object
- `index`: `int` - index of the transaction
- `height`: `int` - height of the block where this transaction was in
- `hash`: `[]byte` - hash of the transaction

## [TxSearch](https://github.com/tendermint/tendermint/tree/master/rpc/core/tx.go?s=5158:5248#L175)
TxSearch allows you to query for multiple transactions results. It returns a
list of transactions (maximum ?per_page entries) and the total count.

```shell
curl "localhost:26657/tx_search?query=\"account.owner='Ivan'\"&prove=true"
```

```go
client := client.NewHTTP("tcp://0.0.0.0:26657", "/websocket")
q, err := tmquery.New("account.owner='Ivan'")
tx, err := client.TxSearch(q, true)
```

> The above command returns JSON structured like this:

```json
{


	  "jsonrpc": "2.0",
	  "id": "",
	  "result": {
		   "txs": [
	      {
	        "proof": {
	          "Proof": {
	            "aunts": [
	              "J3LHbizt806uKnABNLwG4l7gXCA=",
	              "iblMO/M1TnNtlAefJyNCeVhjAb0=",
	              "iVk3ryurVaEEhdeS0ohAJZ3wtB8=",
	              "5hqMkTeGqpct51ohX0lZLIdsn7Q=",
	              "afhsNxFnLlZgFDoyPpdQSe0bR8g="
	            ]
	          },
	          "Data": "mvZHHa7HhZ4aRT0xMDA=",
	          "RootHash": "F6541223AA46E428CB1070E9840D2C3DF3B6D776",
	          "Total": 32,
	          "Index": 31
	        },
	        "tx": "mvZHHa7HhZ4aRT0xMDA=",
	        "tx_result": {},
	        "index": 31,
	        "height": 12,
	        "hash": "2B8EC32BA2579B3B8606E42C06DE2F7AFA2556EF"
	      }
	    ],
	    "total_count": 1
	  }

}
```

### Query Parameters

| Parameter | Type   | Default | Required | Description                                               |
|-----------+--------+---------+----------+-----------------------------------------------------------|
| query     | string | ""      | true     | Query                                                     |
| prove     | bool   | false   | false    | Include proofs of the transactions inclusion in the block |
| page      | int    | 1       | false    | Page number (1-based)                                     |
| per_page  | int    | 30      | false    | Number of entries per page (max: 100)                     |

### Returns

- `proof`: the `types.TxProof` object
- `tx`: `[]byte` - the transaction
- `tx_result`: the `abci.Result` object
- `index`: `int` - index of the transaction
- `height`: `int` - height of the block where this transaction was in
- `hash`: `[]byte` - hash of the transaction

## [UnconfirmedTxs](https://github.com/tendermint/tendermint/tree/master/rpc/core/mempool.go?s=6497:6565#L242)
Get unconfirmed transactions (maximum ?limit entries) including their number.

```shell
curl 'localhost:26657/unconfirmed_txs'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:26657", "/websocket")
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

### Query Parameters

| Parameter | Type | Default | Required | Description                          |
|-----------+------+---------+----------+--------------------------------------|
| limit     | int  | 30      | false    | Maximum number of entries (max: 100) |
```

## [UnsafeDialPeers](https://github.com/tendermint/tendermint/tree/master/rpc/core/net.go?s=1782:1868#L75)
## [UnsafeDialSeeds](https://github.com/tendermint/tendermint/tree/master/rpc/core/net.go?s=1279:1348#L62)
## [Unsubscribe](https://github.com/tendermint/tendermint/tree/master/rpc/core/events.go?s=4191:4285#L139)
Unsubscribe from events via WebSocket.

```go
client := client.NewHTTP("tcp://0.0.0.0:26657", "/websocket")
err := client.Unsubscribe("test-client", query)
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
| query     | string | ""      | true     | Query       |

<aside class="notice">WebSocket only</aside>

## [UnsubscribeAll](https://github.com/tendermint/tendermint/tree/master/rpc/core/events.go?s=5037:5120#L172)
Unsubscribe from all events via WebSocket.

```go
client := client.NewHTTP("tcp://0.0.0.0:26657", "/websocket")
err := client.UnsubscribeAll("test-client")
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

<aside class="notice">WebSocket only</aside>

## [Validators](https://github.com/tendermint/tendermint/tree/master/rpc/core/consensus.go?s=1045:1112#L46)
Get the validator set at the given block height.
If no height is provided, it will fetch the current validator set.

```shell
curl 'localhost:26657/validators'
```

```go
client := client.NewHTTP("tcp://0.0.0.0:26657", "/websocket")
state, err := client.Validators()
```

The above command returns JSON structured like this:

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



---

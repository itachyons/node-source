## iTachyons PoA Blockchain

A customized version of the Go Ethereum project with block rewards for the Proof-of-Authority Clique Consenus Engine. Automated builds are available for stable releases

## Building the source

For prerequisites and detailed build instructions please check the iTachyons documentation [Installation Instructions](https://docs.itachyons.io/setting-up-your-own-node/installation).

Building `tacnode` requires both a Go (version 1.16 or later) and a C compiler. You can install
them using your favourite package manager. Once the dependencies are installed, run

```shell
make tacnode
```

or, to build the full suite of utilities:

```shell
make all
```

## Executables

The itachyons node comes with several wrappers/executables found in the `cmd`
directory.

|    Command    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| :-----------: | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|  **`tacnode`**   | Our main iTachyons CLI client. It is the entry point into the iTachyons network (thoth), capable of running as a full node (default), archive node (retaining all historical state) or a light node (retrieving data live). It can be used by other processes as a gateway into the iTachyons network via JSON RPC endpoints exposed on top of HTTP, WebSocket and/or IPC transports.           |
|   `clef`    | Stand-alone signing tool, which can be used as a backend signer for `geth`.  |
|   `devp2p`    | Utilities to interact with nodes on the networking layer, without running a full blockchain. |
|   `abigen`    | Source code generator to convert iTachyons contract definitions into easy to use, compile-time type-safe Go packages. It operates on plain Contract ABIs with expanded functionality if the contract bytecode is also available. However, it also accepts Solidity source files, making development much more streamlined.  |
|  `bootnode`   | Stripped down version of our iTachyons client implementation that only takes part in the network node discovery protocol, but does not run any of the higher level application protocols. It can be used as a lightweight bootstrap node to aid in finding peers in private networks.                                                                                                                                                                                                                                                                 |
|     `evm`     | Developer utility version of the EVM that is capable of running bytecode snippets within a configurable environment and execution mode. Its purpose is to allow isolated, fine-grained debugging of EVM opcodes (e.g. `evm --code 60ff60ff --debug run`).                                                                                                                                                                                                                                                                     |
|   `puppeth`   | a CLI wizard that aids in creating a new iTachyons network.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |

### Hardware Requirements

Minimum:

* VPS running latest version of Ubuntu
* 2 CPU Cores
* 200GB SATA (RAID 1 is recommended)
* 4 GB RAM
* A broadband Internet connection with upload/download speeds of at least 100Mbps
* At least 1 TB bandwidth per month

Recommended:

* VPS or dedicated server running latest version of Ubuntu
* 4 CPU Cores
* 200GB SSD
* 8 GB RAM
* A broadband Internet connection with upload/download speeds of at least 150Mbps
* At least 1 TB bandwidth per month

### Full node on the main iTachyons network - Thoth

By far the most common scenario is people wanting to simply interact with the iTachyons
network: create accounts; transfer funds; deploy and interact with contracts. For this
particular use-case the user doesn't care about years-old historical data, so we can
sync quickly to the current state of the network. To do so:

```shell
$ tacnode console
```

This command will:
 * Start `tacnode` in snap sync mode (default, can be changed with the `--syncmode` flag),
   causing it to download more data in exchange for avoiding processing the entire history
   of the iTachyons network, which is very CPU intensive.
 * Start up `tacnode`'s built-in interactive Javascript Console,
   (via the trailing `console` subcommand) through which you can interact using web3 methods 
   (note: the `web3` version bundled within `tacnode` is very old, and not up to date with official docs),
   as well as `tacnode`'s own management APIs.
   This tool is optional and if you leave it out you can always attach to an already running
   `tacnode` instance with `tacnode attach`.


### Configuration

As an alternative to passing the numerous flags to the `tacnode` binary, you can also pass a
configuration file via:

```shell
$ tacnode --config /path/to/your_config.toml
```

To get an idea how the file should look like you can use the `dumpconfig` subcommand to
export your existing configuration:

```shell
$ tacnode --your-favourite-flags dumpconfig
```

### Programmatically interfacing `tacnode` nodes

As a developer, sooner rather than later you'll want to start interacting with `tacnode` and the
iTachyons network via your own programs and not manually through the console. To aid
this, `tacnode` has built-in support for a JSON-RPC based APIs.
These can be exposed via HTTP, WebSockets and IPC (UNIX sockets on UNIX based
platforms, and named pipes on Windows).

The IPC interface is enabled by default and exposes all the APIs supported by `tacnode`,
whereas the HTTP and WS interfaces need to manually be enabled and only expose a
subset of APIs due to security reasons. These can be turned on/off and configured as
you'd expect.

HTTP based JSON-RPC API options:

  * `--http` Enable the HTTP-RPC server
  * `--http.addr` HTTP-RPC server listening interface (default: `localhost`)
  * `--http.port` HTTP-RPC server listening port (default: `8545`)
  * `--http.api` API's offered over the HTTP-RPC interface (default: `eth,net,web3`)
  * `--http.corsdomain` Comma separated list of domains from which to accept cross origin requests (browser enforced)
  * `--ws` Enable the WS-RPC server
  * `--ws.addr` WS-RPC server listening interface (default: `localhost`)
  * `--ws.port` WS-RPC server listening port (default: `8546`)
  * `--ws.api` API's offered over the WS-RPC interface (default: `eth,net,web3`)
  * `--ws.origins` Origins from which to accept websockets requests
  * `--ipcdisable` Disable the IPC-RPC server
  * `--ipcapi` API's offered over the IPC-RPC interface (default: `admin,debug,eth,miner,net,personal,txpool,web3`)
  * `--ipcpath` Filename for IPC socket/pipe within the datadir (explicit paths escape it)

You'll need to use your own programming environments' capabilities (libraries, tools, etc) to
connect via HTTP, WS or IPC to a `tacnode` node configured with the above flags and you'll
need to speak JSON-RPC on all transports. You
can reuse the same connection for multiple requests!

**Note: Please understand the security implications of opening up an HTTP/WS based
transport before doing so! Hackers on the internet are actively trying to subvert
iTachyons nodes with exposed APIs! Further, all browser tabs can access locally
running web servers, so malicious web pages could try to subvert locally available
APIs!**

## License

The iTachyons library (i.e. all code outside of the `cmd` directory) is licensed under the
[GNU Lesser General Public License v3.0](https://www.gnu.org/licenses/lgpl-3.0.en.html),
also included in our repository in the `COPYING.LESSER` file.

The iTachyons binaries (i.e. all code inside of the `cmd` directory) is licensed under the
[GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.en.html), also
included in our repository in the `COPYING` file.

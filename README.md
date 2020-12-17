# wash
```
                               _____ _                 _    _____ _          _ _
                              / ____| |               | |  / ____| |        | | |
 __      ____ _ ___ _ __ ___ | |    | | ___  _   _  __| | | (___ | |__   ___| | |
 \ \ /\ / / _` / __| '_ ` _ \| |    | |/ _ \| | | |/ _` |  \___ \| '_ \ / _ \ | |
  \ V  V / (_| \__ \ | | | | | |____| | (_) | |_| | (_| |  ____) | | | |  __/ | |
   \_/\_/ \__,_|___/_| |_| |_|\_____|_|\___/ \__,_|\__,_| |_____/|_| |_|\___|_|_|
```
wasmCloud Shell - A single CLI to handle all of your wasmCloud tooling needs

## Installing wash
```
cargo install wash-cli
```

## Available subcommands
<pre>
- claims    Generate and manage JWTs for wasmCloud Actors
- ctl       Interact with a wasmCloud control interface
- keys      Utilities for generating and managing keys
- par       Create, inspect, and modify capability provider archive files
- reg       Interact with OCI compliant registries
- up        Launch wasmCloud REPL environment
</pre>

## Using wash

### Signing a wasmCloud actor (`claims sign`, `claims inspect`)
```
# We'll be using `echo` from https://github.com/wasmCloud/examples for this example actor

# Navigate to the root of your actor project (where Cargo.toml resides)
cd examples/echo

# Build your actor
cargo build --release

# Sign your actor
# Here we're signing the Echo actor with the http_server capability, as that actor handles HTTP requests.
wash claims sign target/wasm32-unknown-unknown/debug/echo.wasm --http_server --name Echo
```
1. Navigate to your project directory, we'll be using `echo` from https://github.com/wasmCloud/examples
1. Run `cargo build` to build your `wasm` under `target/wasm32-unknown-unknown/debug/echo.wasm`
1. A signed wasmCloud actor is bundled with claims, which includes verifiable information about the source, signer, and capabilities that an actor is allowed to leverage. Required information to minimally sign an actor are:
    1. `issuer`, or `Account` key to verify the source of the actor
    1. `subject`, or `Module` key to uniquely identify the actor by public key
    1. `expiration`, in days, of the claims
    1. `nbf`, or `not-before-days` of the claims
    1. `version`, a human readable version string of the actor
    1. `name`, a human readable name for th actor
    1. List of capabilities that the actor needs to run. The first-party wasmCloud capability contract IDs are present as flags to `wash claims sign`, but you can supply your own capabilities with `--cap <capability-contract_id>`
1. For our example, we only need to supply the capabilities, and the actor name. `wash` takes care of supplying default values for `expiration`, `nbf`, `version`, and will also generate you an `Account` and `Module` key to store in `$WASH_HOME` (`$HOME/.wash/keys`)
1. Run `wash claims sign target/wasm32-unknown-unknown/debug/echo.wasm --http_server --name Echo`, replacing `echo` with your project name. `wash` will output information about your generated `Account` key, and the `Module` key for this actor.
1. Done! You can find your signed actor in `target/wasm32-unknown-unknown/debug/echo_s.wasm`. Try inspecting it to look at the claims embedded in the actor `wash claims inspect target/wasm32-unknown-unknown/debug/echo_s.wasm`

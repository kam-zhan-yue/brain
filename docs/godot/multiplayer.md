# Authority
# RPC
Remote Procedure Calls (RPC) are functions that can be called on other peers. These can be created using the `@rpc` annotation before a function definition.

```gdscript
func _ready():
    if multiplayer.is_server():
        print_once_per_client.rpc()

@rpc
func print_once_per_client();
    print("I will be printed once per client")
```

For a remote call to be successful, the sending and receiving node need to have the same `NodePath`, which means they must have the same name. 

> If a function is annotated with `@rpc` on the client script, then this function must also be declared on the server script. Both RPCs must have the same signature which is evaluated with a checksum of all RPCs. All RPCs in a script are checked at once, and all RPCs must be declared on the client and server scripts, even if the are not in use.

The `@rpc` annotation can take a number of arguments. `@rpc` is equivalent to:
```gdscript
@rpc("authority", "call_remote", "reliable", 0)
```
- `mode`
    - `authority`: Only the multiplayer authority can call remotely. The authority is the server by deafult, but can be changed per-node using the `Node.set_multiplayer_authority`
    - `any_peer`: Clients are allowed to call remotely. Useful for transferring user input
- `sync`
    - `call_remote`: The function will not be called on the local peer
    - `call_local`: The function can be called on the local peer. Useful when the server is also a player.
- `transfer_mode`:
    - `unreliable`: Packets are not acknowledged, can be lost, and can arrive at any order
    - `unreliable_ordered`: Packets are received in the order they were sent in. This is achieved by ignoring packets that arrive later if another that was sent after them has already been recieved. Can cause packet loss if used incorrectly.
    - `reliable`: Resent attempts are sent until packets are acknowledged, and their order is preserved. Has a significant performance penaly.
- `transfer_channel` is the channel index.

# Channels
Modern networking protocols support channels, which are separate connections within the connection. This allows for multiple streams of packets that do not interfere with each other. 

For example, game chat related messages and some of the core gameplay messages should be send reliably, but a gameplay message should not wait for a chat message to be acknowledged. This is achieved using different channels.

- Channels are also useful when used with the unreliable ordered transfer mode
- Sending packets of variable size with this transfer mode can cause packet loss
- Separating them to multiple streams of homogenous packets by using chennls allows ordered transfer with little packet loss and without the latency of reliable mode

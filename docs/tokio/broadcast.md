[See Documentation](https://docs.rs/tokio/latest/tokio/sync/broadcast/index.html)

A multi-producer, multi-consumer broadcast queue. Each sent value is seen by all consumers.

A `Sender` is used to broadcast values to all connected `Receiver` values. `Sender` handles are clone-able, allowing concurrent send and receive actions.

When a value is sent, all `Receiver` handles are notified and will receive the value. The value is stored once inside the channel and cloned on demand for each receiver. Once all receivers have received a clone of the value, the value is released from the channel.

A channel is created by calling `channel`, specifying the maximum number of messages the channel can retain at any given time.

New `Receiver` handles are created by calling `Sender::subscribe`. The returned `Receiver` will receive values after the call to `subscribe`.

### Lagging

As send messages must be retained until all `Receiver` handles receive a clone, broadcast channels are susceptible to the "slow receiver" problem. In this case, all but one receiver are able to receive the values at the rate they are sent. Because one receiver is stalled, the channel starts to fill up.

The broadcast channel implementation handles this by setting a hard upper bound on the number of values the channel may retain at any given time. The upper bound is passed into the `channel` function as an argument.

If a value is sent when the channel is at capacity, the oldest value currently held by the channel is released, freeing up spaces for the new value.

### Closing

When all `Sender` handles have been dropped, no new values may be sent. At this point, the channel is "closed". Once a receiver has received all values retained by the channel, the next call to `recv` will return with `RecvError::Closed`.

When a `Receiver` handle is dropped, any messages not read by the receiver will be marked as read.

### Basic Usage
```rust
use tokio::sync::broadcast;

#[tokio::main]
async fn main() {
    let (tx, mut rx1) = broadcast::channel(16);
    let mut rx2 = tx.subscribe();

    tokio::spawn(async move {
        assert_eq!(rx1.recv().await.unwrap(), 10);
        assert_eq!(rx1.recv().await.unwrap(), 20);
    });

    tokio::spawn(async move {
        assert_eq!(rx2.recv().await.unwrap(), 10);
        assert_eq!(rx2.recv().await.unwrap(), 20);
    });

    tx.send(10).unwrap();
    tx.send(20).unwrap();
}
```

### Handling Lag
```rust
use tokio::sync::broadcast;

#[tokio::main]
async fn main() {
    let (tx, mut rx) = broadcast::channel(2);

    tx.send(10).unwrap();
    tx.send(20).unwrap();
    tx.send(30).unwrap();

    // The receiver lagged behind
    assert!(rx.recv().await.is_err());

    // At this point, we can abort or continue with lost messages

    assert_eq!(20, rx.recv().await.unwrap());
    assert_eq!(30, rx.recv().await.unwrap());
}
```
# kiteconnect-rs
[![Crates.io](https://img.shields.io/crates/v/kiteconnect.svg)](https://crates.io/crates/kiteconnect)
[![Travis](https://img.shields.io/travis/zerodhatech/kiteconnect-rs/master.svg)](https://travis-ci.org/zerodhatech/kiteconnect-rs/)

API wrapper for kiteconnect in rust


## Docs

https://docs.rs/kiteconnect

## Usage

Head on to https://crates.io/crates/kiteconnect

Copy `kiteconnect = "<VERSION>"` dependency to Cargo.toml file


### KiteConnect REST APIs

```rust
extern crate kiteconnect;
extern crate serde_json as json;

use kiteconnect::connect::KiteConnect;

fn main() {
    let kiteconnect = KiteConnect::new("<API-KEY>", "<ACCESS-TOKEN>");
    let holdings: json::Value = kiteconnect.holdings().unwrap();
    println!("{:?}", holdings);
}
```

### Kite Ticker Websocket

```rust
extern crate kiteconnect;
extern crate serde_json as json;

use kiteconnect::ticker::{KiteTicker, KiteTickerHandler, WebSocketHandler}

#[derive(Debug)]
struct CustomHandler {
    count: u32
}

impl KiteTickerHandler for CustomHandler {
    fn on_open<T>(&mut self, ws: &mut WebSocketHandler<T>)
    where T: KiteTickerHandler {
        // Subscribe to a list of tokens on opening the websocket connection
        ws.subscribe(vec![123456]);
        println!("Fellow on_open callback");
    }
    fn on_ticks<T>(&mut self, ws: &mut WebSocketHandler<T>, tick: Vec<json::Value>)
    where T: KiteTickerHandler {
        println!("{:?}", tick);
        println!("Fellow on_ticks callback");
    }

    fn on_close<T>(&mut self, ws: &mut WebSocketHandler<T>)
    where T: KiteTickerHandler {
        println!("Fellow on_close callback");
    }

    fn on_error<T>(&mut self, ws: &mut WebSocketHandler<T>)
    where T: KiteTickerHandler {
        println!("Fellow on_error callback");
    }
}

fn main() {
    let mut ticker = KiteTicker::new("<API-KEY>", "<ACCESS-TOKEN>");

    let custom_handler = CustomHandler {
        count: 0
    };

    ticker.connect(custom_handler, None);

    loop {}
}

```

## Running Examples

### KiteConnect REST API sample

```bash
cargo run --example connect_sample
```

### KiteConnect Websocket sample
```bash
cargo run --example ticker_sample
```

## TODO
- [ ] Add serializer structs for all kiteconnect returning datastructures
- [ ] Reconnection mechanism

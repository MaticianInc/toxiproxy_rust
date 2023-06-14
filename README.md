## Toxiproxy - Rust client

[![Build Status][ci-badge]][ci-url]
[![Crates.io][crates-badge]][crates-url]
[![Docs][docs-badge]][docs-url]

[ci-badge]: https://travis-ci.org/itarato/toxiproxy_rust.png?branch=master
[ci-url]: https://travis-ci.org/itarato/toxiproxy_rust
[crates-badge]: https://img.shields.io/crates/v/toxiproxy_rust.svg
[crates-url]: https://crates.io/crates/toxiproxy_rust
[docs-url]: https://docs.rs/toxiproxy_rust/0.1.6/toxiproxy_rust/
[docs-badge]: https://img.shields.io/docsrs/toxiproxy_rust

Rust client for [Toxiproxy](https://github.com/Shopify/toxiproxy).

## Usage

Populating proxies:

```rust
let proxies = TOXIPROXY.populate(vec![
  Proxy::new(
    "socket_service".into(),
    "localhost:2001".into(),
    "localhost:2000".into(),
  ),
  Proxy::new(
    "redis".into(),
    "localhost:6000".into(),
    "localhost:6379".into(),
  )
])?;
```

Testing with an unavailable connection:

```rust
TOXIPROXY.find_and_reset_proxy("redis")?.with_down(|| {
  // Calling the desired service...
})?;
```

Testing with toxics (for full documentation on available toxics see [the original docs](https://github.com/Shopify/toxiproxy#toxics)):

```rust
TOXIPROXY.find_and_reset_proxy("redis")?.with_latency("downstream".into(), 2000, 0, 1.0).apply(|| {
  // Calling the desired service...
})?;
```

Or without a safe lambda (that takes care of resetting a proxy):

```rust
TOXIPROXY.find_proxy("redis")?.with_latency("downstream".into(), 2000, 0, 1.0)
// Calling the desired service...

TOXIPROXY.find_proxy("redis")?.disable();
// Test unavailability.
TOXIPROXY.find_proxy("redis")?.enable();
```

Supported toxics:

- [latency](https://github.com/Shopify/toxiproxy#latency)
- [down](https://github.com/Shopify/toxiproxy#down)
- [bandwith](https://github.com/Shopify/toxiproxy#bandwith)
- [slow close](https://github.com/Shopify/toxiproxy#slow_close)
- [timeout](https://github.com/Shopify/toxiproxy#timeout)
- [slicer](https://github.com/Shopify/toxiproxy#slicer)
- [limit data](https://github.com/Shopify/toxiproxy#limit_data)

Using a custom address for Toxiproxy server:

```rust
let toxiclient: Client = toxiproxy_rust::Client::new("1.2.3.4:5678");
```

## Development

Tests:

```bash
$> cargo test -- --test-threads 1
```

# Strategy

| curve25519 Backend   | Baseline cmp | ed25519 Releases |
| :---                 | :---         | :---             |
| [default] serial u32 | --           | 1.x*, 2.x        | 
| [default] serial u64 | u32          | 1.x, 2.x         |
| fiat_u32             | u64          | 2.x              |
| fiat_u64             | fiat_u32**   | 2.x              |
| simd avx2            | fiat_u64**   | 1.x, 2.x         |

* *The u32 backend was broken in 1.x
* **The fiat backend was not available in 1.x - comparison is against u64

# 1.x Series

| Report | Description   | Snapshots |
| :---   | :---          | :--- |
| avx2   | avx2 over u64 | nightly-x86_64-unknown-linux-gnu [2022-14-1858-000](1.x/nightly-x86_64-unknown-linux-gnu/2022-14-1858-000/criterion/report/index.html)

* Broken: env cargo +nightly bench --features "u32_backend batch" > ~/ed25519-dalek-bench-u32.txt 2>&1 ; \

```sh
cargo +nightly bench --features batch > ~/ed25519-dalek-bench-u64.txt 2>&1 ; \
env RUSTFLAGS='-C target_feature=+avx2' cargo +nightly bench --features "simd_backend batch" > ~/ed25519-dalek-bench-avx2.txt 2>&1
```

# 2.x Series

| Report   | Description        | Snapshots                             |
| :---     | :---               | :---                                  |
| u32      | Default serial u32 | nightly-x86_64-unknown-linux-gnu [2022-14-1858-000](2.x/nightly-x86_64-unknown-linux-gnu/2022-14-1858-000/criterion/u32-baseline/report/index.html)  |
| u64      | Default serial u64 | nightly-x86_64-unknown-linux-gnu [2022-14-1858-000](2.x/nightly-x86_64-unknown-linux-gnu/2022-14-1858-000/criterion/u64-delta-u32/report/index.html) |
| fiat_u32 | With fiat u32      | nightly-x86_64-unknown-linux-gnu [2022-14-1858-000](2.x/nightly-x86_64-unknown-linux-gnu/2022-14-1858-000/criterion/fiat_u32-delta-u64/report/index.html) |
| fiat_u64 | With fiat u64      | nightly-x86_64-unknown-linux-gnu [2022-14-1858-000](2.x/nightly-x86_64-unknown-linux-gnu/2022-14-1858-000/criterion/fiat_u64-delta-fiat_u32/report/index.html) |
| avx2 | With simd avx2         | nightly-x86_64-unknown-linux-gnu [2022-14-1858-000](2.x/nightly-x86_64-unknown-linux-gnu/2022-14-1858-000/criterion/avx2-delta-fiat_u64/report/index.html) |

## Linux

Tear down/up

In the to-be-tested crate directory
```
rm -rf target
rm -rf /tmp/ed25519-bench-results
mkdir -p /tmp/ed25519-bench-results/u32-baseline
mkdir -p /tmp/ed25519-bench-results/u64-delta-u32
mkdir -p /tmp/ed25519-bench-results/fiat_u32-delta-u64
mkdir -p /tmp/ed25519-bench-results/fiat_u64-delta-fiat_u32
mkdir -p /tmp/ed25519-bench-results/avx2-delta-fiat_u64
```

u32 Baseline
```sh
env RUSTFLAGS='--cfg curve25519_dalek_bits="32"' cargo +nightly bench --features batch > ~/ed25519-dalek-bench-u32.txt 2>&1 ; \
cp -R target/criterion/* /tmp/ed25519-bench-results/u32-baseline/ ; \
```

u64 over u32 baseline
```sh
cargo +nightly bench --features batch > ~/ed25519-dalek-bench-u64.txt 2>&1 ; \
cp -R target/criterion/* /tmp/ed25519-bench-results/u64-delta-u32/ ; \
```

fiat_u32 over u64
```sh
env RUSTFLAGS='--cfg curve25519_dalek_backend="fiat" --cfg curve25519_dalek_bits="32"' cargo +nightly bench --features batch > ~/ed25519-dalek-bench-fiat_u32.txt 2>&1 ; \
cp -R target/criterion/* /tmp/ed25519-bench-results/fiat_u32-delta-u64/ ; \
```

fiat_u64 over fiat_u32
```sh
env RUSTFLAGS='--cfg curve25519_dalek_backend="fiat"' cargo +nightly bench --features batch > ~/ed25519-dalek-bench-fiat_u64.txt 2>&1 ; \
cp -R target/criterion/* /tmp/ed25519-bench-results/fiat_u64-delta-fiat_u32/ ; \
```

simd +avx2 over fiat_u64
```sh
env RUSTFLAGS='-C target_feature=+avx2 --cfg curve25519_dalek_backend="simd"' cargo +nightly bench --features batch > ~/ed25519-dalek-bench-avx2.txt 2>&1
cp -R target/criterion/* /tmp/ed25519-bench-results/avx2-delta-fiat_u64
```
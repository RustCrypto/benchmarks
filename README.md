# benchmarks-dalek
Dalek family benchmarks

curve25519_dalek

## Mac M1 - aarch64

```
env RUSTFLAGS='--cfg curve25519_dalek_bits="32"' cargo +nightly bench --features rand_core > ~/bench-u32.txt 2>&1 ; \
cargo +nightly bench --features rand_core > ~/bench-u64.txt 2>&1 ; \
env RUSTFLAGS='--cfg curve25519_dalek_backend="fiat" --cfg curve25519_dalek_bits="32"' cargo +nightly bench --features rand_core > ~/bench_fiat_u32.txt 2>&1 ; \
env RUSTFLAGS='--cfg curve25519_dalek_backend="fiat"' cargo +nightly bench --features rand_core > ~/bench_fiat_u64.txt 2>&1 ; \
env RUSTFLAGS='-C target_feature=+neon --cfg curve25519_dalek_backend="simd"' cargo +nightly bench --features rand_core > ~/bench_neon.txt 2>&1
```

ed25519-dalek


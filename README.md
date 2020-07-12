# CloudFlare worker speed test

See https://community.cloudflare.com/t/cloudflare-workers-very-slow-with-moderate-sized-webassembly-bindings/184668/12

This worker does one very simple thing it substitutes the variable `{{ name }}` in a string and returns that string.

By default the template is `Hello, {{ name }}` and the value of name is `world`, both `template` and `name` can
be customised using get parameters.

The substitution can be done in the the following ways:
* in javascript using `replace`
* in rust use string `replace`
* in rust using [regex](https://crates.io/crates/regex) `replace_all`
* use a tera [tera](https://crates.io/crates/tera) `one_off`

Using the following means of substitution varies the was module from non existing, from non existent, to small, 
medium and large.

## Performance results

| mode                                   | wasm size (kb) | mean response time (ms) | response stdev (ms) |
|----------------------------------------|----------------|-------------------------|---------------------|
| javascript                             |              0 |                      84 |                   8 |
| simple replace, wee_alloc              |             20 |                     127 |                  26 |
| simple replace, no wee_alloc           |             25 |                     133 |                  30 |
| regex, wee_alloc, default-features off |            193 |                     540 |                 235 |
| regex, wee_alloc                       |            619 |                    1228 |                 188 |
| tera, wee_alloc, default-features off  |           1160 |                    2744 |                 452 |
| tera, wee_alloc                        |           2546 |                    2844 |                 496 |

![results plot](https://github.com/samuelcolvin/cloudflare-worker-speed-test/blob/master/results.png?raw=true)


Results are calculated using `test_performance.py` script above, the worker is modified by switching features
in `Cargo.toml`, for the "javascript" case, `mode` has to be changed in `wrangler.toml` and some line commented and
uncommented in `worker/worker.js`.

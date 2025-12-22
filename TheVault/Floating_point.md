There are 2 types of `flaoting-point` types

##### 1. REAL
- `float4` is an alias
- also, if given an argument `p` such as `1 ≤ p ≤ 24`, then `float(p)` maps as an alias of `real`
- takes `4` bytes
- Range: `[1e-37  1e+37]`
- supports `6` digits after decimal

##### 2. DOUBLE PRECISION
- `float8` is an alias
- if given an argument `p` such as `25 ≤ p ≤ 53`, then `float(p)` maps to double precision
- takes `8` bytes
- Range: `[1e-307  1e+308]`
- supports `15` digits after decimal

> calculation involving these floating point numbers should be made in such a way that very minute error is allowed
> Example
```bash
>>> SELECT 7.0::float8 * (2.0 / 10.0);
      ?column?
--------------------
 1.4000000000000001
(1 row)
```
> as you can see, there is a 1 at the end and not exactly 1.4


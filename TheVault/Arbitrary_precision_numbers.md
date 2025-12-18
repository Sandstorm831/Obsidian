> Perfectly precise and completely accurate but is very slow as compared to `Integers` and `Floating-point` numbers

- `DECIMAL` as an alias
- `NUMERIC` without any parameter can handle any range of number, also bigger than `BIGINT`
- `NUMERIC` accepts two parameters → `NUMERIC(precision, scale)`:
	- Ex : 12.345 → Here, `precision: 5` & `scale: 3`
	- `precision` is total number of digits
	- `scale` is total number of digits on right side of decimal
- Can't exceed total number of digits allotted to the left side of decimal, which is equal to `precision - scale`
- Can exceed the total number of digits on right side of decimal as defined by scale, but in that case the number will be an rounded-off to the number of digits allowed by `scale`
- Example:
  ![[numeric_1.png]]
  ![[numeric_2.png]]

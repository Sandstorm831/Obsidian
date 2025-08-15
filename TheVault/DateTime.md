`datetime` library is used primarily to manipulate dates and times

- to convert a `datetime` string in any valid `ISO 8601` format to `datetime` obj
```python
from datetime import datetime
from datetime import timezone

datetime_str = "2025-08-08 10:03:36.419645+00"
# It's a valid string

# It converts the datetime_str to datetime_obj
datetime_obj = datetime.fromisoformat(datetime_str)
# datetime.datetime(2025, 8, 8, 10, 3, 36, 419645, tzinfo=datetime.timezone.utc)
.
# get an isoformtted datetime_str 
datetmie_iso_str = datetime_obj.isoformat()
# '2025-08-08T10:03:36.419645+00:00'

# to get_rid of microseconds
datetime_iso_str = datetime_obj.isoformat(timespec="seconds")
# '2025-08-08T10:03:36+00:00'

# To get a datetime obj of current time in UTC
curr_utc_datetime = datetime.now(timezone.utc)
# datetime.datetime(2025, 8, 14, 14, 0, 29, 734473, tzinfo=datetime.timezone.utc)
```

- To get a timezone other than `UTC`
	```python
	from zoneinfo import ZoneInfo
	IST = ZoneInfo("Asia/Kolkata")
	datetime.now(IST).isoformat(timespec="seconds")
	# '2025-08-16T01:17:22+05:30'
	```
	
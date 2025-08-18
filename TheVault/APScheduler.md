- basic `BlockingScheduler` with `2` hours interval schedule
```python
from apscheduler.schedulers.background import BlockingScheduler
from apscheduler.triggers.interval import IntervalTrigger
from apscheduler.executors.pool import ThreadPoolExecutor
from time import sleep

executor = {
	"name": ThreadPoolExecutor(max_workers=20)
}
trigger = IntervalTrigger(hours=2)
scheduler = BlockingScheduler(executors=executor, daemon = False)

def func(a: str):
	# function to be scheduled
	print("Doing something with: ", a)
	sleep(10)
	print("Done")

dummy = "dummy"
scheduler.add_job(func=func, args = (dummy,) ,trigger = trigger)
scheduler.start()
```
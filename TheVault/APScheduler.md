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

- If you want to handle error in `apscheduler` you can create a `listener func`, and attach that to the scheduler with `EVENT_JOB_ERROR` mask
	```python
from apscheduler.events import EVENT_JOB_ERROR, JobExecutionEvent

func listener(event: JobExecutionEvent):
	if event.exception:
		exception = event.exception
		traceback = event.traceback
		return_val = event.retval
		# <do_whatever_you_want_to_do_with_error>

scheduler.add_listener(listener, mask = EVENT_JOB_ERROR)
	```
	The scheduler will not stop the schedule until you call `scheduler.shutdown()` on the scheduler
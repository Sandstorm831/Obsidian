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

- If you want to kill the jobs that were running from the last time till now, it's not explicitly provided, so we improvise
```python
### somfile.py
from multiprocessing import Queue
QueueStore = Queue()


### main.py
from somefile import QueueStore
from somemodule import func
import os
import signal

def schedule_cancellable_job(an-arg):
	global QueueStore # it is a multiprocessing.Queue that you import
	if not QueueStore.empty():
		pid = QueueStore.get()
		try:
			os.kill(pid, signal.SIGKILL)
		except ProcessLookupError as _:
			pass
		
	process = multiprocessing.Process(target=func, args=(an-arg,))
	process.start()
	QueueStore.put(process.pid)
	process.join()
```

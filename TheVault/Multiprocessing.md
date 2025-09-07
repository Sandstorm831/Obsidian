- decorator that can run any process in a separate sub-process
	```python
from multiprocessing import Process, Queue
import functools

def multi_proc(func):
	@functools.wrap(func)
	def wrapper(*args, **kwargs):
		ipc_queue = Queue()
		def f(queue, *args, **kwargs)
			try:
				result = func(*args, **kwargs)
				queue.put((True, result)) # res have to serializable
			except Exception as e:
				queue.put((False, e))
		sub_proc = Process(target=f, args = (ipc_queue, *args), kwargs=kwargs)
		sub_proc.start()
		sub_proc.join()
		status, res = ipc_queue.get()
		ipc_queue.close()
		if status:
			return res
		else:
			raise res
	return wrapper
	```
	the `ipc_queue` can only contain `serialisable` elements

- I don't know much about this issue, but I faced it so I am writing it here, there can be a case when you are trying to start the `Process` and at the same time, at some place of your code, it tries to write to `stdout` channel, then, there is a chance of a race-condition and following error might bubble
	```bash
reentrant call inside <_io.BufferedWriter name='<stdout>'>
	```
	In such scenarios, wrap the `Process.start()` in a `try-except` block like this, also mark initialisation in `try-except` block only.
```python
from time import sleep

while True:
	try:
		proc = multiprocessing.Process(target=func)
		proc.start()
		break
	except RuntimeError as e:
		sleep(0.1)
		continue
```
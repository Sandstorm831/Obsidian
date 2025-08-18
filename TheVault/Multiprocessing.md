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
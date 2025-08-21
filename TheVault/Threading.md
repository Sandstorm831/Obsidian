There are following objects that threading module have and can be useful for different works
- Thread
	```python
	import threading
	
	def func(name: str = "hello"):
		# do something
		print(f"{name}, I am doing something")
		return
	
	# create a thread
	# args for positional arguments
	# kwargs for named arguments
	Thread = threading.thread(target=func, args = (args,), kwarsgs=kwargs)
	
	# start the thread
	Thread.start()
	
	# To make sure main program don't exit before the thread completes
	Thread.join() # This will block the thread from where it is called 
	# untill the Thread on which join is called completes
	
```
- Lock
```python
import threading

# Get a Lock
is_locked = threading.Lock()

# This lock is not owned by any thread, and it can be acquired (locked)
# by any thread, and can be released by any. If someone acquired the lock
# then if anyone other thread tries to acquire the lock will be blocked
# untill the lock is released by someone
is_locked.acquire()
is_locked.release()

# to check if someone acquired the lock
is_locked.locked() # returns True if lock is acquired
```

- Threads can spawn `multiprocessing.Process` thus giving them ability to create highly nested flows of `threads` and `processes` running in parallel

- If a `lock` is `acquired` then use the `try ... finally` block to `release` the `lock` otherwise, in an error situation, there might be a case when `lock` is not released

- errors from `thread` are not propagated to the `main thread`. If you have to manage threads there are few options I thought for it:
	- `manager decorator`: define a manager decorator that will handle all the logic for handling errors and paste it on top of the `target` function
	- `subclass threading.Thread`: you can create a `subclass` inheriting from `threading.Thread` and override the `run` function according to your needs
	- `Queue`: propagate the error using queues and catch
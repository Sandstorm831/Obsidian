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


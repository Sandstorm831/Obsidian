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

- If you have a lot of short-lived tasks which is **not** compute intensive and primarily IO-bound, and you don't want to have explicit control over each thread, then go with `ThreadPoolExecutor`
	```python
from concurrent.futures import ThreadPoolExecutor
from time import sleep

executor = ThreadPoolExecutor(max_workers = 5, thread_name_prefix = "pool_")

def sleeping(i):
	sleep(i)
	print(f"I slept {i}")

if __name__ == "__main__":
	executor.submit(sleeping, 5)
	executor.submit(sleeping, 4)
	executor.submit(sleeping, 6)
	executor.submit(sleeping, 2)
	executor.submit(sleeping, 9)
	
	```
	here, if more than tasks are assigned then the number of threads, the the tasks are queued and will be allocated as soon as a new thread becomes available, as you can see in following example
	```python
def printer(i):  
	print("I am starting: ", i)  
    sleep(i)  
    print("I slept: ", i)
   
executor = ThreadPoolExecutor(max_workers = 3)
for i in range(2,8):
	executor.submit(printer,i)

####### result
# I am starting:  2  
# <Future at 0x792ccb0983e0 state=running>  
# I am starting:  3  
# <Future at 0x792cccabe930 state=running>  
# I am starting:  4  
# <Future at 0x792ccb075d60 state=running>  
# <Future at 0x792ccb075af0 state=pending>  
# <Future at 0x792ccb075880 state=pending>  
# <Future at 0x792ccb0758b0 state=pending>  
# I slept:  2  
# I am starting:  5  
# I slept:  3  
# I am starting:  6  
# I slept:  4  
# I am starting:  7  
# I slept:  5  
# I slept:  6  
# I slept:  7
	```
	
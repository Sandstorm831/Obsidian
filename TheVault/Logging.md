- basics
	- `logger`: glue class that puts everything together and you use in your app to log outputs using `logger.info("message")`. It passes the `messages` to handlers according to the severity `level` of logger
		```python
		import logging
		logger = logging.getLogger(__name__)   # named logger
		logger.debug("debug message")
		logger.info("info message")
		logger.warning("warning message")
		logger.error("error message")
		logger.critical("error message")
		```
	- `handlers`: It directs the message according to definition of handler, tha is, it can direct it `stdout` channel,  `file`, `email`, `http/tcp` socket, `http/udp` socket and many others. you can attach levels to the handler below which the handler won't react. It has to attached to the logger and levels can be attached. types of handlers:
		- `StreamHandler`: sending output to a channel such as `stdout` or `stderr`
		- `TimedRotatingFileHandler`: sending output to a file with managing it
		- `SMTPHandler`: sending output as an email
		- `QueueHandler`: Sending output to a `queue`, useful in multi-processing apps
		- `QueueListener`: used in conjunction to `QueueHandler`, listens to `Queue` and passes the messages to the handlers attached to it, ***not a handler in true sense***.
		```python
		import logging
		import sys
		logger = logging.getLogger() # root logger
		logger.setLevel(logging.DEBUG)
		console_handler = logging.StreamHandler(stream=sys.stdout)
		console_handler.setLevel(logging.DEBUG)
		logger.addHandler(console_handler)
		```
	- `formatters`: It tells how to format the message. it have two attributes
		- `fmt`: a string for formatting, defined using following tags:
			- `%(asctime)s` => local time in format: `2005-03-19 15:10:26,618`
			- `%(levelname)s` => level of the message
			- `%(name)s` => name of the logger used to log the call
			- `%(processName)s` => name of the process (if available)
			- `%(threadName)s` => name of the thread (if available)
			- `%(message)s` => message
		- `datefmt`: a string for formatting `%(asctime)s` of `fmt` attribute
		```python
		formatter = logging.Formatter(
			fmt="%(asctime)s - %(levelname) - [%(threadName)s] - %(message)s",
			datefmt="%Y-%m-%dT%H:%M:%S%z"
		)
		console_handler.setFormatter(formatter)
		```
	- `filter`: It basically filters the message which should be logged or not, not very useful until now

- Few things for everything to make-sense:
	- we attach all the handlers to root handler, but we never log directly to root handlers
	- `logging.getLogger()` is root logger, whereas `logging.getLogger(__name__)`or`logging.getLogger("any_name") are named loggers
	- we use named loggers to log messages, and the messages are propagated up in the hierarchy
	- root logger sits at the top of hierarchy, so everything reaches root logger

- A programme to give idea how to configure most useful loggers, handlers and formatters
```python
import logging
from logging.handlers import (
	SMTPHandler,
	TimedRotatingFileHandler,
	QueueHandler,
	QueueListener
)
import sys
from multiprocessing import Queue

def get_handlers():
	log_file_path = "path_to_file"
	mailhost = ("smtp.gmail.com", 587) # host can change
	mail_from = "from@email.address"
	mail_to = ["to@email.address"]
	mail_creds = ("username", "password")
	
	gen_str = "%(asctims)s - %(name)s - %(message)s"
	email_str = "%(levelname)s - (asctime)s\n%(message)s"
	general_formatter = logging.Formatter(fmt=gen_str)
	email_formatter = logging.Formatter(fmt=email_str)
	
	email_handler = SMTPHandler(
		mailhost = mailhost,
		fromaddr = mail_from,
		toaddrs = mail_to,
		subject = "EMAIL SUBJECT",
		credentials = mail_creds,
		secure = (),      # IMP: this option will upgrade connection to TLS
		timeout = 30,
	)
	email_handler.setFormatter(email_formatter)
	email_handler.setLevel(logging.CRITICAL)
	
	console_handler = StreamHandler(stream = sys.stdout)
	console_handler.setLevel(logging.INFO)
	console_handler.setFormatter(gen_formatter)
	
	# the following handler will create a new log file everyday, and 
	# log to that file for whole day, will save that file by name of 
	# date after midnight and create a new file for current day, it will
	# store max 15 files and if 16th is created, a rollover occurs
	# which remove the oldest backup file
	file_handler = TimedRotatingFileHandler(
		filename = log_file_path,
		interval = 1,  # sets length of `when` parameter, thus every 1 midnight
		when = "midnight",  # sets the time to rollover
		backupCount = 15,   # store max 15 backup files
		encoding = "utf-8"
	)
	file_handler.setLevel(logging.DEBUG)
	file_handler.setFormatter(gen_formatter)
	
	return [console_handler, file_handler, email_handler]

# have to be called just once in the app
def setup_main_logging(multi_proc_queue):
	root_logger = logging.getLogger()
	root_logger.setLevel(logging.DEBUG)
	handlers = get_handlers()
	# this will attach all the handlers to the queue listener
	listener = QueueListener(
		multi_proc_uque,
		*handlers,
	)
	# when a message is put into queue, listener will dispatch 
	# that message to all the handlers
	
	# Now we need a handler that will put the message into queue
	q_handler = QueueHandler(multi_proc_queue)
	# checks if a QueueHandler is already present in root_logger handlers
	# we don't attach another if already present
	if not any(isinstance(h, QueueHandler) for h in root_logger.handlers):
		root_logger.addHandler(q_handler)
	
	return listener

# have to be called in every multiprocessing.Process
def setup_proc_logging(multi_proc_queue):
	root_logger = logging.getLogger()
	root_logger.setLevel(logging.DEBUG)
	q_handler = QueueHandler(multi_proc_queue)
	if not any(isinstance(h,QueueHandler) for h in root_logger.handlers):
		root_logger.q_handler


# if you don't use any multiprocessing module functionality, you don't need
# to attach any of QueueListener and QueueHandler, instead you only have to
# attach the handlers you get from get_handlers() one by one

# to use this logger, call setup_main_logging() as the very beginning
# of entry point of your app, making sure it only runs once.
# then you can just make named loggers in each of your files as usual
# and use them, whereas each multiprocessing.Process should also call 
# setup_proc_logging() at the starting and then can use named logger
# declared at top of the file where the function is placed
# EXAMPLE

# main.py
from server import func
setup_main_logging()
logger = logging.getLogger(__name__)
logger.debug("a debug message")

func()

# service.py
logger = logging.getLogger(__name__)

def func():
	logger.info("some info message")
	pass

# multi.py
logger = logging.getLogger(__name__)

def multi_proc_func():
	setup_proc_logging()
	
	
	logger.debug("another debug message")
```
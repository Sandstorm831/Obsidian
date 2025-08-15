- Basics
	```python
from fastapi import FastAPI, Request
from uvicorn import Server, Config

app = FastAPI()

@app.post("/")
def method(request: Request):
	print("recieved request at '/' endpoint")

config = Config(app = app, host="127.0.0.1", port=3985, log_level = "info")
server = Server(config)
server.run()
	```
	This will start a simple `FastAPI`server 

- If you want to pass a variable to the methods, you can define a state
```python
app = FastAPI()
app.state.variable = value

@app.get("/path")
def method(request: Request):
	req_value = request.app.state.variable
	print(req_value)
```

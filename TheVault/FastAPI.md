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

- There are `tags` in `FastAPI` that just used to pass the metadata, doesn't seemed quite useful
- `dependencies` in `FastAPI` are like little brother of middlewares, they are used to do certain actions before some intended routes or in a `APIRouter`
- How to split routes between different files: `APIRouter`
```python
## main.py
from fastapi import FastAPI
from uvicorn import Server, Config
from src.helper import reportRouter

app = FastAPI()
app.include_router(reportRouter, prefix = "/api/v1")

## start the server
# . . .

## src/helper.py
from fastAPI import APIRouter

reportRouter = APIRouter()
@reportRouter.post("/submit")
async def(param1, param2):
	# do something
	return {"status": "success"}

```

- `Pydantic models` helps to structure the data you'll be receiving by framing them in a `class`
```python
from pydantic import BaseClass

class MyDataClass(BaseClass):
	data_1: str
	data_2: float
	data_3: int
	...
	data_n: bool
```

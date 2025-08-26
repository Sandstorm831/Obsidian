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
	**Always run the `FastAPI` server from the root of the project**
	
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

- Here's an example of a `server` that accepts a `multipart/form-data` which contain `files` and `form data`
```python
from fastapi import FastAPI, Form, Depends, File
from typing import Annotated
from pydantic import BaseModel
import pickle

app = FastAPI()
class YourModelClass(BaseModel):
	field1: str
	field2: int
	field3: str | None = None

async def convertToYourModel(
	field1: str = Form(...), # this ... elipsis marks the field required
	field2: int = Form(...),
	field3: str | None = Form(None)  # this field is optional
):
	return YourModelClass(
		field1 = field1,
		field2 = field2,
		field3 = field3
	)

@app.post("/route")
async def handler(
	model_data: Annotated[YourModelClass, Depends(convertToYourModel)],
	upload_file: UploadFile = File(...)
):
	data_dict = dict(model_data)
	file_name = upload_file.filename
	file_bytes = await upload_file.read()
	combined_dict = {
		"filename": file_name,
		"filebytes": file_bytes,
		"data": data_dict
	}
	serialized_data: bytes = pickle.dumps(combined_dict)
	return {
		"status": "success",
	}
```
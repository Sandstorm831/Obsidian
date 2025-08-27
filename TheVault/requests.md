- attaching files
```python
import requests

files = {"file": open("path_to_file", "rb")}

# To explicitly set filename, content_type and headers
files = {
		"file": ( 
			"name_of_file", 
			open("path_to_file", "rb"), 
			"any_headers", 
			{"header": "info"},
		)
}

# example uploading an png image
files = {"file": ("myimg.png", open("./my_img.png", "rb"), "image/png" )}

# the request's content_type is multipart/form-data
requests.post("https://example.com/", files=files)

# for multiple-files, it will be a list of tuples (field_name, file_info)
multiple_files = [
	("images": ("myimg.png", open("./my_img.png", "rb"), "image/png" )),
	("images": ("foo.png", open("./foo.png", "rb"), "image/png" )),
]

requests.post("https://example.com/", files=multiple_files)
```

- attaching files via `in-memory buffer`
```python
# open("file", "rb") gives a binary stream, which can read by 
# requests module at the time of making requests
import io
from PIL import Image

buffer = io.BytesIO() # binary_stream

image = Image.open("path_to_image.png") # PIL Image object

image.save(buffer, format = image.format) # save image in binary in the buffer

buffer.seek(0) # set the buffer stream position to beginning

files = {"file": ("filename.png", buffer, "image/png)}
request.post("https://example.com/", files=files)
# the image would be read at the time of making request from the buffer
```

- a standard function for `retry` and `error handling` capabilities, try using this instead of direct `requests` methods
```python
from urllib3.util.retry import Retry
from requests.adapters import HTTPAdapter
import requests

def request_with_retry(
	url: str = None,
	method: str = "GET",
	params = None,
	data = None,
	files = None
	headers = None,
	retries: int = 5,
	timeout: int = 10,
	backoff_factor: float = 0.5,
	**kwargs
):
	session = requests.Session()
	# retry module manages the temporary spontaneous error thrown
	# by the network and raises them after all retries is exhauseted
	# wrapping them in MaxRetryError
	retry_strategy = Retry(
		total=retries, # max number of retries
		backoff_factor=backoff_factor, 
		# scaling factor for the delay between retries
		# sleep -> {backoff_factor} * (2 ** (num_of_prev_retries))
		status_forcelist = [429, 500, 502, 503, 504],
		# statuses on which retries needs to be done
		allowed_methods = None, # methods on which to retry, None mean on each
		raise_on_status = False
	)
	adapter = HTTPAdapter(max_retries = retry_startegy)
	session.mount("http://", adapter)
	session.mount("https://", adapter)
	response = session.request(
		method = method,
		url = url,
		params = params,
		data = data,
		headers = headers,
		files = files,
		timeout = timeout,
		allow_redirects = False, # we'll manually manage redirects
		**kwargs
	)
	while response.status_code == 301 or response.status_code == 302:
		print(f"redirecting to {response.headers["Location"]}")
		response = session.request(
			method = method,
			url = response.headers["Location"],
			params = params,
			data = data,
			headers = headers,
			files = files,
			timeout = timeout,
			allow_redirects = False,
			**kwargs
		)
	response.raise_for_status()
	return response
	 
```
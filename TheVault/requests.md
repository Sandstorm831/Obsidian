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

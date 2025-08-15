Library for adding image processing

- opening an image
```python
from PIL import Image

imageObj = Image.open("path/to/image.ext", formats=["PNG", "JPEG"])
# formats include list of formats that needs to be tried
# to determind the format of image

imageBuffer = open("path/to/image.ext", "rb")
imageObj = Image.open(imageBuffer, formats=["PNG", "JPEG"])
# here buffer objects can also be passed as shown
```
- Save image as a file or as a buffer
```python
imageObj.save("filename.jpg/png", format="JPEG/PNG")

import io
buffer = io.BytesIO()
imageObj.save(buffer, format="JPEG/PNG")
```
- cropping image
```python
cropped_img_obj = imageObj.crop((xmin, ymin, xman, ymax))
# here first two numbers are co-ordinates of top-left corner
# and last two are co-ordinates of bottom-right corner
```

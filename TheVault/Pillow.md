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
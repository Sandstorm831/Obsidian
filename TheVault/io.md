- `open` gives a buffered_reader object which is a binary stream
```python
bufferedReader = open("filename", "rb") # binary_strea

# to read the bytes in the buffered object
bufferedReader.read()
# b'xc1\x87\x89m ...... x00IEND\xaeB`\x82'

# buffered stream position is not at the end
bufferedReader.read()
# b''

# pull buffered stream position back to beginning
bufferedREader.seek(0)
bufferedReader.read()
# b'xc1\x87\x89m ...... x00IEND\xaeB`\x82'
```

- binary stream stored in in-memory bytes buffer
```python
import io
buffer = io.BytesIO()

# reading buffer
buffer.read()
# b''
```
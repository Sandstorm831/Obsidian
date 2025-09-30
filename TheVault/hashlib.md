For getting a hash
- get a hash object
- feed this object with bytes using `update` method
- you can get the hash/digest of concatenated data fed so far by `digest()` and `hexdigest()` methods

example: obtain the digest of byte string `nobody is here` using `sha256`
```python
import hashlib
m = hashlib.sha256()
m.update(b"nobody")
m.update(b" is here")
m.digest()
# b'j\xe3\x12\xf2\xf9\x99\xe61a\xc6\x81Pj\xe5pw\xb1\xc1\xe5x \x92\xe4\x08\x97\x8a9\n\xc2\xdfv\xe8'
m.hexdigest()
# '6ae312f2f999e63161c681506ae57077b1c1e5782092e408978a390ac2df76e8'
```
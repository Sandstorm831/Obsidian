- here's an example of how you can use `cryptography` library in python to implement `ECC`
```python
# pip install cryptography

from cryptography.hazmat.primitives.asymmetric import ed25519
from cryptography.hazmat.primitives import serialization

private_key = ed25519.Ed25519PrivateKey.generate()
public_key = priv.public_key()

readable_private_key = private_key.private_bytes(
	serialization.Encoding.PEM,
	serialization.PrivateFormat.PKCS8,  # std format to store private keys
	serialization.NoEncryption()   # no password protection
).decode() # to convert the whole thing into readable text

readable_public_key = public_key.public_bytes(
	serialization.Encoding.PEM,
	serialization.PublicFormat.SubjectPublicKeyInfo # std container for pub key
).decode()


```
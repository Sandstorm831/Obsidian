Code to encrypt and de-crypt using `AESGCC`
- encryption
```python
from cryptography.hazmat.primitives.ciphers.aead import AESGCM
import os, base64, dotenv


env_path = ".env"
raw_env = dotenv.dotenv_values(dotenv_path=env_path)
key = os.urandom(32)
b64_key = base64.b64encode(key).decode()

AESGCM_KEY = base64.b64decode(b64_key)

aesgcm = AESGCM(key=AESGCM_KEY)
encrypt_env_path = env_path
  
  

def encrypt_env_value(plaintext: str | None, key: bytes) -> str | None:
	if plaintext is None:
		return None
	aesgcm = AESGCM(key)
	nonce = os.urandom(12)
	ct = aesgcm.encrypt(nonce, plaintext.encode(), associated_data=None)
	return base64.b64encode(nonce + ct).decode()


encrypt_env = {k: encrypt_env_value(v, AESGCM_KEY) for k, v in raw_env.items()}
for k, v in encrypt_env.items():
	if v is None:
		continue
	dotenv.set_key(dotenv_path=encrypt_env_path, key_to_set=k, value_to_set=v)
```
- decryption
```python
from cryptography.hazmat.primitives.ciphers.aead import AESGCM
import base64, dotenv, os

  
env_path = ".env"
raw_env = dotenv.dotenv_values(dotenv_path=env_path)
boxenv_path = "/env/box.env"
raw_key = dotenv.dotenv_values(dotenv_path=boxenv_path).get("AESGCM_KEY")

if raw_key is None:
	raise ValueError("key unavailable for env decryption")

AESGCM_KEY = base64.b64decode(raw_key)
aesgcm = AESGCM(key=AESGCM_KEY)

  
  

def decrypt_env_value(enc_b64: str | None) -> str | None:
	if enc_b64 is None:
		return None
	data = base64.b64decode(enc_b64)
	nonce = data[:12]
	ct_and_tag = data[12:]
	plaintext = aesgcm.decrypt(nonce, ct_and_tag, associated_data=None)
	return plaintext.decode()

  
  

for k, v in raw_env.items():
	decrypted = decrypt_env_value(v)
	if decrypted is not None:
		os.environ[k] = decrypted
```
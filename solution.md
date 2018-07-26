
# Creating Addresses


```python
# Address Example

from helper import encode_base58, hash160, double_sha256
sec = bytes.fromhex('025CBDF0646E5DB4EAA398F365F2EA7A0E3D419B7E0330E39CE92BDDEDCAC4F9BC')
h160 = hash160(sec)
raw = b"\x00" + h160
raw = raw + double_sha256(raw)[:4]
addr = encode_base58(raw)
print(addr)
```

### Try it

#### 7.1. Find the mainnet and testnet addresses corresponding to the private keys:
```
compressed, 888**3
uncompressed, 321
uncompressed, 4242424242
```


```python
from ecc import G

from helper import double_sha256, encode_base58, hash160

components = (
    # (compressed, secret)
    (True, 888**3),
    (False, 321),
    (False, 4242424242),
)

# iterate through components
for compressed, secret in components:
    # get the public point
    point = secret * G
    # get the sec format
    sec = point.sec(compressed)
    # hash160 the result
    h160 = hash160(sec)
    # prepend b'\x00' for mainnet b'\x6f' for testnet
    for prefix in (b'\x00', b'\x6f'):
        # raw is the prefix + h160
        raw = prefix + h160
        # get the double_sha256 of the raw, first 4 bytes are the checksum
        checksum = double_sha256(raw)[:4]
        # append checksum
        total = raw + checksum
        # encode_base58 the whole thing
        print(encode_base58(total).decode('ascii'))
```

### Test Driven Exercise


```python
from ecc import S256Point, G
from helper import double_sha256, encode_base58, hash160

class S256Point(S256Point):

    
    def address(self, compressed=True, testnet=False):
        '''Returns the address string'''
        # get the sec
        sec = self.sec(compressed)
        # hash160 the sec
        h160 = hash160(sec)
        # raw is hash 160 prepended w/ b'\x00' for mainnet, b'\x6f' for testnet
        if testnet:
            prefix = b'\x6f'
        else:
            prefix = b'\x00'
        raw = prefix + h160
        # checksum is first 4 bytes of double_sha256 of raw
        checksum = double_sha256(raw)[:4]
        # encode_base58 the raw + checksum
        address = encode_base58(raw+checksum)
        # return as a string, you can use .decode('ascii') to do this.
        return address.decode('ascii')
```

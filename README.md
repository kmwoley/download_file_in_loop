# Download random files in a loop and test their SHA1 hashes (using the Backblaze B2 API)

This script takes two inputs on the command line:
1. A B2 directory location (pretending that folders exist)
3. The number of files that should be downloaded.

For any download attempt where the SHA1 from the B2 response AND the SHA1 of the 
downloaded file contents doesn't match, request response information will be output to help 
troubleshoot why the hash does not match.

Every other request inserts an HTTP Range header (Range: bytes=0-9999999), in 
case this could be related. 

This script was written to attempt to debug the issue described here: https://github.com/restic/restic/issues/3268

## Pre-requisites
This scripts requires: 

Python3

yaml
```
pip install pyyaml
```

requests
```
pip install requests
```

b2 API
```
pip install b2sdk
```

## Config
Configuration is stored in config.yaml.

```yaml
bucketName : '[bucket name here]'
keyid      : '[add keyid here]'
appkey     : '[add appkey here]'
apiVersion : '/b2api/v2/'
```

## Running the script
```bash
$ python start.py [directory] [num of requests] 
```

## Sample successful output
```
$ python start.py data 2

[Sun, 14 Feb 2021 20:34:19 GMT]: Starting downloads, 2 times
[Attempt: 0]: Request made at Mon, 14 Feb 2021 20:34:19 GMT
[Attempt: 0]: File: data/45/456272d1036c670c21aaaa866c3c40831038e8e566554dd93dbaaaaab4e2b8e2
[Attempt: 0]: All SHA1 match.
[Attempt: 1]: Request made at Mon, 14 Feb 2021 20:34:20 GMT
[Attempt: 1]: File: data/e3/e3ef89e0c406080d55a41b372f7079a9e36093760dc66afd52418e14e39d6aaa
[Attempt: 1]: All SHA1 match.
[Sun, 14 Feb 2021 20:34:21 GMT]: End
```

## Sample failed output
```
$ python start.py data 1

...
[Attempt: 0]: Request made at Mon, 15 Feb 2021 22:45:31 GMT
[Attempt: 0]: File: data/d5/d5e0721625f61d3181b7d1133ta9832d148e96d9e7f8fe455bf1584018c9a5f1
[Attempt: 0]: SHA1 do not match.
[Attempt: 0]: b2_content_sha1: 09dba9d711a3a6aaa789c5b87767c44da9f838b4
[Attempt: 0]: res_content_sha1: 56790675aaa7f0555fa49661d90ca60b5c1bf6fa
[Attempt: 0] REQUEST  **********
GET https://f002.backblazeb2.com/file/bucketname/data/d5/d5e0721625f61d3181b7d1133ta9832d148e96d9e7f8fe455bf1584018c9a5f1
Authorization: [omitted]
User-Agent: python-requests/2.25.1
Accept-Encoding: gzip, deflate
Accept: */*
Connection: keep-alive

[Attempt: 0] RESPONSE  **********
HTTP/11 200
Cache-Control: max-age=0, no-cache, no-store
x-bz-file-name: data/d5/d5e0721625f61d3181b7d1133ta9832d148e96d9e7f8fe455bf1584018c9a5f1
x-bz-file-id: 4_abcdefg0b1afd28c77f090317_abcde870a57ce859f_d5ef00229_m111036_g502_v11111129_t446
x-bz-content-sha1: 09dba9d711a3a6aaa789c5b87767c44da9f838b4
X-Bz-Upload-Timestamp: 1591052436000
Accept-Ranges: bytes
Content-Type: application/octet-stream
Content-Length: 4690000
Date: Mon, 15 Feb 2021 22:45:32 GMT

...
```

Here are the basic `CurL` commands that can be used.
```bash
curl -X <METHOD> <URL> [OPTIONS]
```

- `METHOD`: `GET`, `POST`, `PUT`, `DELETE` 
- `URL`: Attach query parameters in the URL only
- `[OPTIONS]`: All other things including headers, body, file_upload, form

Examples:
1. Simple Get request
```bash
curl -X GET https://api.example.com/users
curl https://api.example.com/users # If you omit -X GEt, curl defaults to GET
```
2. Headers are written as `-H "Header-Name: value`
```bash
curl -X POST https://api.example.com/login \
-H "Content-Type: application/json" \
-H "Authorization: Bearer TOKEN123"
# you can add as many headers as you want in this way
```
3. body is given by `-d` or `--data` flag, thing to remember here is if your data have symbol `@` anywhere then use `--data-raw`, `--data` have a special interpretation of `@` and treats everything after it as a file. However don't use it for file uploads as files will be corrupted or not properly interpreted.
	```bash
curl -X POST https://api.example.com/login \
-H "Content-Type: application/json"
-d '{"username": "rg", "password": "password"}'
	```
	Also,`Content-Type: aplication/x-www-form-urlencoded` is the way `-d`/`--data` or `--data-raw` pass the data to the server. User the appropriate header to send the data the way you want. In the above example, we are passing data as `json` using the appropriate header

4. For `multipart/form-data` format use `-F "<name>=<value>`, File upload can also be specified here as by using symbol `@`. Use it to upload files whenever you have to.
```bash
curl -X POST https://api.example.com/upload \
-F "username=rg"
-F "my_file=@./myphoto.jpg"
```
5. For saving the response to a particular file `-o path/to/file`
```bash
curl -X GET https://api.example.com/users/list
-o ./myfile.txt
```

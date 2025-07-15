# Build & Test Tools
## Python
### Build Executable
```bash
# Single executable
pyinstaller --onefile main.py --name xxx

# Windowed app with resources
pyinstaller --noconfirm --onefile --windowed main.py \
    --name xxx \
    --add-data "notes/xxx.pdf;notes"
```

### Run Tests
```bash
python -m unittest -v tests/test_xxx.py
```

## Golang
### Cross-Compilation
```bash
# Linux
GOOS=linux go build main.go

# macOS
GOOS=darwin go build main.go

# Windows
GOOS=windows go build main.go
```

### Testing Commands
```bash
go test         # Basic test
go test -v      # Verbose output

# Directory tests
go test ./calculation/ -v

# Specific test suite
go test ./calculation/ -run NameOfStruct -v

# Bypass cache
go test ./calculation/ -count=1 -run SqlTestSuite -v
```

---

# HTTP Tools
## Fake APIs
- **JSONPlaceholder**: `https://jsonplaceholder.typicode.com/`
- **JSON Server**: [`https://github.com/typicode/json-server`](https://github.com/typicode/json-server)
- **JSON-to-Go**: [`https://mholt.github.io/json-to-go/`](https://mholt.github.io/json-to-go/)

## JSON Server Setup
```bash
npm install -g json-server
```
> **Note**: Install Node.js from [https://nodejs.org/en/download/](https://nodejs.org/en/download/) if encountering issues

## Start JSON Server
```bash
json-server --watch db.json
```
> **Windows Fix**: Run `Set-ExecutionPolicy Unrestricted` if blocked

---

## HTTP Client (http.rest)
### GET Request
```http
GET http://localhost:3000/book/
```

### GET Single Item
```http
GET http://localhost:3000/book/1
```

### POST Request
```http
POST http://localhost:3000/book/
Content-Type: application/json

{
    "title": "Angels and Demons",
    "author": "Dan Brown",
    "rating": 5
}
```

### DELETE Request
```http
DELETE http://localhost:3000/book/1
```

---

## cURL Examples
### Get All Albums
```bash
curl http://localhost:8080/albums
```

### Get Album by ID
```bash
curl http://localhost:8080/albums/2
```

### Create Album
```bash
curl http://localhost:8080/albums \
    --include --header \
    "Content-Type: application/json" \
    --request "POST" --data \
    '{"ID": "4", "Title": "Title_4", "Price": 40}'
```

### POST Request
```bash
curl --header "Content-Type: application/json" \
    --request POST \
    --data '{"email": "name.surname@myrl.tech", "firstName": "Name", "lastName": "Surname"}' \
    https://...
```

### PUT Request
```bash
curl --header "Content-Type: application/json" \
    --request PUT \
    --data '{"email": "name.surname@myrl.tech", "firstName": "Name2", "lastName": "Surname2"}' \
    https://...
```

### DELETE Request
```bash
curl -X DELETE "https://...?email=name.surname@myrl.tech"
```

---

# AWS Commands
## Create IAM Role
```bash
aws iam create-role \
    --role-name lambda-ex \
    --assume-role-policy-document file://trust-policy.json
```

## Attach IAM Policy
```bash
aws iam attach-role-policy \
    --role-name lambda-ex \
    --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole
```

## Zip Function
```bash
zip function.zip main
```

## Delete Lambda Function
```bash
aws lambda delete-function \
    --function-name go-lambda-function
```

## Create Lambda Function
```bash
aws lambda create-function --function-name go-lambda-function \
    --zip-file fileb://function.zip \
    --handler main \
    --runtime go1.x \
    --role arn:aws:iam::XXXXX:role/lambda-ex
```

## Invoke Lambda
```bash
aws lambda invoke \
    --function-name go-lambda-function \
    --cli-binary-format raw-in-base64-out \
    --payload '{"what is your name?": "Jim","How old are you": 25}' output.txt
```
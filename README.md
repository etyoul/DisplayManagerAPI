# DisplayManager MessageAPI

## 1. Overview

본 문서는 WUXIOPTO DisplayManager를 통해 서비스 중인 디스플레이앱에 메시지 송출하는  API의 규격을 공유하기 위해 작성되었습니다.



WUXIOPTO DisplayManager와 동일한 PC에서 사용되어야 하며, 특수한 경우 동일한 네트워크 내에서 사용이 가능하나 WUXIOPTO DisplayManager가 설치된 PC의 IP를 host로 해야 API를 이용할 수 있습니다.



## 2. URL

**포트는 9496입니다.**

**WUXIOPTO DisplayManager가 설치된 PC에서 호출하는 경우**

```http
http://localhost:9496/display/message
```

**동일한 네트워크의 다른 PC에서 호출하는 경우**

```http
http://{IP}:9496/display/message
```







## 3. Method

MessageAPI는 **POST**만을 사용합니다.

GET, PUT, DELETE Method는 지원하지 않습니다.



## 4. HTTP Header

MessageAPI는 JSON 포맷의 데이터로 요청, 응답합니다. 따라서 header의 Content-Type은 다음과 같습니다.

```
Content-Type:application/json
```



## 5. Request

요청은 body에 JSON 데이터로 작성합니다. 각 키는 다음과 같습니다.

* target: 디스플레이 식별값 또는 디스플레이 이름 (DisplayManager에 보이는 이름)
* message: 차량번호를 포함한 팝업 출력 메시지



### 5.1 요청샘플

#### 5.1.1 cURL

```curl
curl -X POST -H "Content-Type: application/json" 
  -d '{"target":"display15982", "message":"주차장에 오신 것을 환영합니다.\n00거 0000차량이 입차되었습니다."}'
  "http://localhost:9496/display/message"
```



#### 5.1.2 Java

```java
OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("application/json");

RequestBody body = RequestBody.create(mediaType, "{\"target\":\"display15982\", \"message\":\"주차장에 오신 것을 환영합니다.\\n00거 0000차량이 입차되었습니다.\"}");

Request request = new Request.Builder()
 .url("http://localhost:9496/display/message")
 .post(body)
 .addHeader("content-type", "application/json")
 .build();
 
Response response = client.newCall(request).execute();
```



#### 5.1.3 Javascript

```javascript
var data = JSON.stringify({
  "target": "display15982",
  "message": "주차장에 오신 것을 환영합니다.\n00거 0000차량이 입차되었습니다."
});

var xhr = new XMLHttpRequest();
xhr.withCredentials = true;

xhr.addEventListener("readystatechange", function () {
  if (this.readyState === 4) {
    console.log(this.responseText);
  }
});

xhr.open("POST", "http://localhost:9496/display/message");
xhr.setRequestHeader("content-type", "application/json");

xhr.send(data);

```



#### 5.1.4 PHP

```php
<?php

$request = new HttpRequest();
$request->setUrl('http://localhost:9496/display/message');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'content-type' => 'application/json'
));

$request->setBody('{"target":"display15982", "message":"주차장에 오신 것을 환영합니다.\\n00거 0000차량이 입차되었습니다."}');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}

```



#### 5.1.5 Python

```python
import requests

url = "http://localhost:9496/display/message"

payload = "{\"target\":\"display15982\", \"message\":\"주차장에 오신 것을 환영합니다.\\n00거 0000차량이 입차되었습니다.\"}"
headers = {
    'content-type': "application/json"
    }

response = requests.request("POST", url, data=payload, headers=headers)

print(response.text)
```



## 6. Response

JSON포맷이며 키는 다음과 같습니다.

* result: success 또는 failure
* resultCode: 0000(성공), 그 외는 실패
* errorMessage: resultCode가 0000이 아닌 경우 오류 메시지가 포함됩니다.



### 6.1 응답예시

#### 6.1.2 성공

```json
{
    "result": "success",
    "resultCode": "0000",
    "errorMessage": ""
}
```



#### 6.1.2 잘못된 데이터로 요청한 경우

```json
{
    "result": "failure",
    "resultCode": "9001",
    "errorMessage": "잘못된 요청입니다."
}
```



#### 6.1.3 등록된 디스플레이가 없는 경우

```json
{
    "result": "failure",
    "resultCode": "1001",
    "errorMessage": "display0000 디스플레이가 없습니다."
}
```



#### 6.1.4 디스플레이가 오프라인인 경우

```json
{
    "result": "failure",
    "resultCode": "1002",
    "errorMessage": "display0000 디스플레이가 연결되어 있지 않습니다."
}
```


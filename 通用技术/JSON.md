# JSON介绍

全称 (JavaScript Object Notation)，JS对象表现形式，为字典形式字符串

在ajax中，允许将复杂格式的响应数据构建成 JSON的格式再进行响应输出

# JSON表现

JSON格式为‘{key:value}’ ，其中 {} 表示单个对象，key(str):value表示属性

|   python    |    json     |
| :---------: | :---------: |
|     str     |   String    |
| int，float  |   Numebr    |
| True，False | true，false |
|    None     |    null     |
| list，tuple |    array    |
|     dic     |   object    |

# 不同文件内JSON和string转换

|                 | python     | js             | flask   |
| --------------- | ---------- | -------------- | ------- |
| JSON --> string | json.dumps | JSON.stringify |         |
| string --> JSON | json.loads | JSON.parse     | jsonify |

# 传输要求

前端：请求头'Content-Type','application/json'

后端：request.get_json['key']
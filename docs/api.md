### api规范
#### (1).获取secret接口

/generate_secret

请求参数：


|参数名|必选|说明|
|---|---|---|
|app|是|app名称,固定字符|
|oparator_id|是|操作人ID|
|oparator_name|是|操作人名称|

响应：
```
{
"result-code": "0",
"version": "1.0",
"cmd": "/generate_secret",
"msg": "OK",
"timestamp": "2016-02-01 19:49:29",
"data": {
    "secret":"sd3lsd3"
}
```
#### (2).推送接口

/push_cards_message

请求参数：


|参数名|必选|说明|
|---|---|---|
|card_id|是|卡片ID|
|card_type|是|卡片类型|
|push_type|是|推送类型0手动1自动|
|app_secret|是|获取的密钥|
|time|是|时间戳|
|sign|是|sign值，验证使用|
|oparator_id|是|操作人ID|
|oparator_name|是|操作人名称|
|operator_time|是|推送时间|

响应：
```
{
"result-code": "0",
"version": "1.0",
"msg": "OK",
"timestamp": "2016-02-01 19:49:29",
"data": {
    
}
说明：result-code 值含意：0 代表推送成功 1001 代表验证权限失败 1002 服务器端响应超时 1003 参数缺失 -1 未知异常
```
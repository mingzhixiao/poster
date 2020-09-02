#### curl的使用

##### 1.get使用

```
curl 47.97.78.69:11087/vshop/wxmp/shopCart/delivery/38470 -H "token:eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiIxMzAxMDMzNTc4MTg2OTM2MzIwMTMwMTAzMzU3ODE4NjkzNjMyMCIsImV4cCI6MTU5OTA2ODc1MSwiY3JlYXRlZCI6MTU5OTAyNTU1MTgxMH0.t7kxA_IMowi71ojXTP8KK5N8SVlMJmp1Ysa7POtcj8DXOQ8iijXGYyRIBXcwEEL6yf_WBK7JchUlubNgQevbYQ" -v

```

##### 2.post使用

```
curl http://47.97.78.69:11091//vshop/web/fileGroup/queryFileGroupList -X POST -H "token:eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiIxODcwNzk4Mjk5OCIsImV4cCI6MTU5OTA2MTk2NiwiY3JlYXRlZCI6MTU5OTAxODc2NjcxNn0.6nG_DfRmJr7w9R6gBDghTGSARLdfdyYtoVssP7Ric2u2xYBxHMTOXoynBPBZ2EwPnBUO7mlyie0TZlun7pZnlw" -H "Content-Type:application/json" -d '{"orders":[{"fieldName":"","order":""}],"page":1,"params":{"shopAdminId":0,"shopId":0},"rows":10}'
```

##### 3.文件上传

```
curl http://47.97.78.69:11091//vshop/web/file/upload -H "token:eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiIxODcwNzk4Mjk5OCIsImV4cCI6MTU5OTA2MTk2NiwiY3JlYXRlZCI6MTU5OTAxODc2NjcxNn0.6nG_DfRmJr7w9R6gBDghTGSARLdfdyYtoVssP7Ric2u2xYBxHMTOXoynBPBZ2EwPnBUO7mlyie0TZlun7pZnlw" -F "file=b.txt"
```


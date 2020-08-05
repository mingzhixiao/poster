### RestTemplate使用方式

##### 1.exchange使用方式

```

    public static void main(String[] args) {
        RestTemplate restTemplate = new RestTemplate();
        String url = "http://192.168.239.105:9018/cshop/web/product/queryProductPage";
        HttpHeaders headers = new HttpHeaders();
        headers.setContentType(MediaType.APPLICATION_JSON_UTF8);
        headers.set("token","eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiIxODg1NTU1NTU1NSIsImV4cCI6MTU5NjU4MDM5MywiY3JlYXRlZCI6MTU5NjUzNzE5MzIyM30.OR02DvPAnLKhxmQH0g7eKis0LVGoe36DHkdnGvglPX7Fy328ic5hegsptOjC9W_xxzHJTQVEM4DiIMimMPCW0w");
        JSONObject jsonObj = new JSONObject();
        jsonObj.put("page",1);
        jsonObj.put("rows",10);

        Map<String,String> hashMap = new HashMap<>();
        hashMap.put("keyword","");
        hashMap.put("productCategoryId", "0");
        hashMap.put("chooseType", "0");
        jsonObj.put("params",hashMap);
        HttpEntity<String> entity = new HttpEntity<>(jsonObj.toString(), headers);
        ResponseEntity<JSONObject> exchange = restTemplate.exchange(url,
                HttpMethod.POST, entity, JSONObject.class);
        exchange.getStatusCode();
        System.out.println(exchange.getBody());
    }
```


### table插件的的三种使用方式

#### 1纯粹的html引入table插件

```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title></title>


    <!-- 引入bootstrap样式 -->
    <link href="https://cdn.bootcss.com/bootstrap/3.3.6/css/bootstrap.min.css" rel="stylesheet">
    <!-- 引入bootstrap-table样式 -->
    <link href="https://cdn.bootcss.com/bootstrap-table/1.11.1/bootstrap-table.min.css" rel="stylesheet">

    <!-- jquery -->
    <script src="https://cdn.bootcss.com/jquery/2.2.3/jquery.min.js"></script>
    <script src="https://cdn.bootcss.com/bootstrap/3.3.6/js/bootstrap.min.js"></script>

    <!-- bootstrap-table.min.js -->
    <script src="https://cdn.bootcss.com/bootstrap-table/1.11.1/bootstrap-table.min.js"></script>
    <!-- 引入中文语言包 -->
    <script src="https://cdn.bootcss.com/bootstrap-table/1.11.1/locale/bootstrap-table-zh-CN.min.js"></script>
</head>
<body>
<table data-toggle="table" data-url="http://127.0.0.1:8888/emps/all">
    <thead>
    <tr>
        <th data-field="id">Item ID</th>
        <th data-field="name">Item Name</th>
        <th data-field="email">Item Price</th>
    </tr>
    </thead>
</table>
</body>
</html>
```

其中data-url 所传递的就是一个List集合 里面的属性包含有id，name email  集合中的对象的属性一定不能少于data-field的列数

#### 2.使用js来简单动态渲染

```html
<body>
<table id="table"></table>
<script>
    $('#table').bootstrapTable({
        url: 'http://127.0.0.1:8888/emps/all',
        columns: [{
            field: 'id',
            title: 'Item ID'
        }, {
            field: 'name',
            title: 'Item Name'
        }, {
            field: 'email',
            title: 'Item Price'
        }, ]
    });
</script>
</body>
```

eg:上面两种简单的方式引入的json的数据如下:

```html
[{"password":"123","set":0,"phone":"","name":"zh","id":4,"email":"3333"},{"password":"1234","set":1,"phone":"1326500449","name":"wzw","id":6,"email":"wzw@foxmail.com"}]
```

#### 3.js复杂渲染（必须掌握）




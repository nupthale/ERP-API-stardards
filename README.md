# API规范

API规范包括路径命名规范、请求方式规范，请求参数规范、返回数据规范与特殊结构规范五部分；

## 路径命名规范

路径的定义主要考虑以下因素：

1. 为了方便快速定位前端文件，目前供应链前端组的页面文件路径是与url的路径完全对应的，如果url中存在变量id，则这种关联就无法实现；所以不建议使用restful的路径命名方式；

2. 一般情况下，路径深度不超过3层，理想情况下都定义三层，如/quotation/manage/list，如果工程所有接口前都以/api或者/backend等开头，则从第二层开始计数，不超过3层，最大长度为/api/quotation/manage/list;

3. 建议每层的命名不超过两个英文单词，两个单词时，命名格式为驼峰，如auditOrder，如果可以一个单词描述清楚就不要使用两个单词组合；

## 请求方式规范

对于资源的操作，请使用以下常用的请求方式（括号中为对应的SQL命令）：

* GET（SELECT）：从服务器取出资源（一项或多项）
* POST（CREATE）：在服务器新建一个资源。
* PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）。
* PATCH（UPDATE）：在服务器更新资源（客户端提供改变的属性，改变部分属性）。
* DELETE（DELETE）：从服务器删除资源。

## 请求参数规范

POST请求时，请求参数可以为FORMDATA或者JSON格式，一般情况下以参数的个数以及复杂度判断使用哪种方式，如果参数都为非对象的简单类型（不包括数组，对象），并且个数小于5个，则使用FORMDATA， 后端使用@RequestParam读取，如果结构复杂，个数较多，则前端参数传JSON格式，后端使用@RequestBody将参数映射为一个对象

## 返回数据规范

包括两部分：http状态码与返回结果数据

### HTTP状态码

* 200 OK - \[GET\]：服务器成功返回用户请求的数据
* 400 INVALID REQUEST - \[POST/PUT/PATCH\]：用户发出的请求有错误，服务器没有进行新建或修改数据的操作
* 401 Unauthorized - \[\*\]：表示用户没有登陆（令牌、用户名、密码错误）
* 403 Forbidden - \[\*\] 表示用户得到授权（与401错误相对），但是访问是被禁止的
* 404 NOT FOUND - \[\*\]：用户发出的请求针对的是不存在的记录，服务器没有进行操作，该操作是幂等的
* 406 Not Acceptable - \[GET\]：用户请求的格式不可得（比如用户请求JSON格式，但是只有XML格式）
* 500 INTERNAL SERVER ERROR - \[\*\]：服务器发生错误，用户将无法判断发出的请求是否成功。

### 返回数据

```
{
    “code”: 200,
    "message": "操作成功",
    "error": ""
}
```

* code: http状态码无法满足需求时，请使用code描述错误类型；200表示成功，错误code如下，无法满足时，请继续按照分类对后面的数字进行扩展

  ```
  用户登陆相关100x:
  1001 用户名或者密码错误
  1002 用户名不存在
  1003 注册时，用户名已经存在
  1004 注册时，存在非法的数据格式
  1005 账户已经被冻结
  1006 验证码错误

  操作相关200x：
  2001 查询/修改/删除/添加/操作失败
  2003 后端服务依赖不可用
  ```

* message：给用户看的错误信息，该信息会展示在用户界面上

* error：给开发看的错误信息，为了方便定位问题，可以将一些错误细节填入该字段，出现问题时，可以快速定位问题； error不在用户界面上显示，开发可以在开发者工具中的请求返回值中查看

* result：如果操作成功，result为请求返回的数据，格式为json对象；常见的结构如下：

  ```
  {
      “result”: {
          "vo1": {},
          "vo2": {}
      }
  }
  如果请求结果为列表，则结构固定为：变量名为list，分页变量为pagination，请不要修改
  {
      "result": {
          "list": [],
          "pagination": {
              "total": 100
          }
      }
  }
  如果需要返回多个列表，则格式为： a,b可替换为有含义的单词
  {
      "result": {
          "aList": [],
          "aPagination": {
              "total": 10
          },
          "bList": [],
          "bPagination": {
              "total": 20
          }
      }
  }
  ```

## 特殊结构规范

1. 为方便后端逻辑处理，前后端日期格式统一为：long型；包括前端传后端的参数和后端回传的数据；
2. 下拉选择的kv命名为id与name，因为regular-ui库的默认值为id和name；
3. 变量命名尽量简短，类名/接口名等已经说明含义的情况下，字段名不要加前缀，如“forwarderQuotationInfoChargeVo”，可以优化为：“infoVo”；
4. 列表需要分页时，前端传给后端的分页信息变量名固定为：pageSize表示一页显示多少条，pageNo当前是第几页；服务端返回的总数变量名为total；老工程可能名称定义的不统一，如果是调用其他工程接口获取到的数据，需要后端转换为此种格式再传给前端；
5. 枚举数字定义从1开始；全部为0，其他、不存在等为-1；boolean意义的枚举，1表示是，0表示否；
6. 表单页面，如下拉选择，传给后端的是Number类型的id，非编辑状态下回显的时候，除了id字段， 还需要返回转换为字符串的变量，这个变量命名统一为variableName\(id类型的变量名后面加Name\)；

## 参考文章

* [restful api设计指南](http://www.ruanyifeng.com/blog/2014/05/restful_api.html)




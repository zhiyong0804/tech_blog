# doc_id&source

#### 手动生成
* PUT 一般来说，是从某些其他的系统中，导入一些数据到es时，会采取这种方式，就是使用系统中已有数据的唯一标识，作为es中document的id

#### 自动生成
* POST 
* 自动生成的id，长度为20个字符，URL安全，base64编码，GUID，分布式系统并行生成时不可能会发生冲突

#### PUTsource元数据和获取指定元数据
```go
put /test_index/test_type/1
{
  "test_field1": "test field1",
  "test_field2": "test field2"
}
GET /test_index/test_type/1?_source=test_field1,test_field2

{
  "_index": "test_index",
  "_type": "test_type",
  "_id": "1",
  "_version": 2,
  "found": true,
  "_source": {
    "test_field2": "test field2"
  }
}
```
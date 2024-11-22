# Nullable & Default​

Milvus allows you to set the `nullable` attribute and default values for scalar fields, except the primary field. For fields marked as `nullable=True`, you can skip the field when inserting data, or set it directly to a null value, and the system will treat it as null without causing an error. When a field has a default value, the system will automatically apply this value if no data is specified for the field during insertion.​

The default value and nullable attributes streamline data migration from other database systems to Milvus by allowing handling of datasets with null values and preserving default value settings. When creating a collection, you can also enable nullable or set default values for fields where values might be uncertain.​

## Limits​{#limits​}

- Only scalar fields, excluding the primary field, support default values and the nullable attribute.​

- JSON and Array fields do not support default values.​

- Default values or the nullable attribute can only be configured during collection creation and cannot be modified afterward.​

- Scalar fields with the nullable attribute enabled cannot be used as `group_by_field` in Grouping Search. For more information about grouping search, refer to [​Grouping Search](https://zilliverse.feishu.cn/wiki/JWZGw89MBiUDBNkhtGfcyyUcnsd).​

- Fields marked as nullable cannot be used as partition keys. For more information about partition keys, refer to [​Use Partition Key](https://zilliverse.feishu.cn/wiki/QWqiwrgJViA5AJkv64VcgQX2nKd).​

- When creating an index on a scalar field with the nullable attribute enabled, null values will be excluded from the index.​

## Nullable attribute​{#nullable-attribute​}

The `nullable` attribute allows you to store null values in a collection, providing flexibility when handling unknown data.​

### Set the nullable attribute​{#set-the-nullable-attribute​}

When creating a collection, use `nullable=True` to define nullable fields (defaults to `False`). The following example creates a collection named `user_profiles_null` and sets the `age` field as nullable:​

<Tabs><TabItem value="Python" label="python" default>

```Python
from pymilvus import MilvusClient, DataType​
​
client = MilvusClient(uri='http://localhost:19530')​
​
# Define collection schema​
schema = client.create_schema(​
    auto_id=False,​
    enable_dynamic_schema=True,​
)​
​
schema.add_field(field_name="id", datatype=DataType.INT64, is_primary=True)​
schema.add_field(field_name="vector", datatype=DataType.FLOAT_VECTOR, dim=5)​
schema.add_field(field_name="age", datatype=DataType.INT64, nullable=True) # Nullable field​
​
# Set index params​
index_params = client.prepare_index_params()​
index_params.add_index(field_name="vector", index_type="IVF_FLAT", metric_type="L2", params={ "nlist": 128 })​
​
# Create collection​
client.create_collection(collection_name="user_profiles_null", schema=schema, index_params=index_params)​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
import io.milvus.v2.client.ConnectConfig;​
import io.milvus.v2.client.MilvusClientV2;​
import io.milvus.v2.common.DataType;​
import io.milvus.v2.common.IndexParam;​
import io.milvus.v2.service.collection.request.AddFieldReq;​
import io.milvus.v2.service.collection.request.CreateCollectionReq;​
​
import java.util.*;​
​
MilvusClientV2 client = new MilvusClientV2(ConnectConfig.builder()​
        .uri("http://localhost:19530")​
        .build());​
        ​
CreateCollectionReq.CollectionSchema schema = client.createSchema();​
schema.setEnableDynamicField(true);​
​
schema.addField(AddFieldReq.builder()​
        .fieldName("id")​
        .dataType(DataType.Int64)​
        .isPrimaryKey(true)​
        .build());​
​
schema.addField(AddFieldReq.builder()​
        .fieldName("vector")​
        .dataType(DataType.FloatVector)​
        .dimension(5)​
        .build());​
​
schema.addField(AddFieldReq.builder()​
        .fieldName("age")​
        .dataType(DataType.Int64)​
        .isNullable(true)​
        .build());​
​
List<IndexParam> indexes = new ArrayList<>();​
Map<String,Object> extraParams = new HashMap<>();​
extraParams.put("nlist", 128);​
indexes.add(IndexParam.builder()​
        .fieldName("vector")​
        .indexType(IndexParam.IndexType.IVF_FLAT)​
        .metricType(IndexParam.MetricType.L2)​
        .extraParams(extraParams)​
        .build());​
​
CreateCollectionReq requestCreate = CreateCollectionReq.builder()​
        .collectionName("user_profiles_null")​
        .collectionSchema(schema)​
        .indexParams(indexes)​
        .build();​
client.createCollection(requestCreate);​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
import { MilvusClient, DataType } from "@zilliz/milvus2-sdk-node";​
​
const client = new MilvusClient({​
  address: "http://localhost:19530",​
  token: "root:Milvus",​
});​
​
await client.createCollection({​
  collection_name: "user_profiles_null",​
  schema: [​
    {​
      name: "id",​
      is_primary_key: true,​
      data_type: DataType.int64,​
    },​
    { name: "vector", data_type: DataType.Int64, dim: 5 },​
​
    { name: "age", data_type: DataType.FloatVector, nullable: true },​
  ],​
​
  index_params: [​
    {​
      index_name: "vector_inde",​
      field_name: "vector",​
      metric_type: MetricType.L2,​
      index_type: IndexType.AUTOINDEX,​
    },​
  ],​
});​
​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
export pkField='{​
    "fieldName": "id",​
    "dataType": "Int64",​
    "isPrimary": true​
}'​
​
export vectorField='{​
    "fieldName": "vector",​
    "dataType": "FloatVector",​
    "elementTypeParams": {​
        "dim": 5​
    }​
}'​
​
export nullField='{​
    "fieldName": "age",​
    "dataType": "Int64",​
    "nullable": true​
}'​
​
export schema="{​
    \"autoID\": false,​
    \"fields\": [​
        $pkField,​
        $vectorField,​
        $nullField​
    ]​
}"​
​
export indexParams='[​
        {​
            "fieldName": "vector",​
            "metricType": "L2",​
            "indexType": "IVF_FLAT",​
            "params":{"nlist": 128}​
        }​
    ]'​
​
curl --request POST \​
--url "${CLUSTER_ENDPOINT}/v2/vectordb/collections/create" \​
--header "Authorization: Bearer ${TOKEN}" \​
--header "Content-Type: application/json" \​
-d "{​
    \"collectionName\": \"user_profiles_null\",​
    \"schema\": $schema,​
    \"indexParams\": $indexParams​
}"​

```

</TabItem></Tabs>

### Insert entities​{#insert-entities​}

When you insert data into a nullable field, insert null or directly omit this field:​

<Tabs><TabItem value="Python" label="python" default>

```Python
data = [​
    {"id": 1, "vector": [0.1, 0.2, 0.3, 0.4, 0.5], "age": 30},​
    {"id": 2, "vector": [0.2, 0.3, 0.4, 0.5, 0.6], "age": None},​
    {"id": 3, "vector": [0.3, 0.4, 0.5, 0.6, 0.7]}​
]​
​
client.insert(collection_name="user_profiles_null", data=data)​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
import com.google.gson.Gson;​
import com.google.gson.JsonObject;​
​
import io.milvus.v2.service.vector.request.InsertReq;​
import io.milvus.v2.service.vector.response.InsertResp;​
​
List<JsonObject> rows = new ArrayList<>();​
Gson gson = new Gson();​
rows.add(gson.fromJson("{\"id\": 1, \"vector\": [0.1, 0.2, 0.3, 0.4, 0.5], \"age\": 30}", JsonObject.class));​
rows.add(gson.fromJson("{\"id\": 2, \"vector\": [0.2, 0.3, 0.4, 0.5, 0.6], \"age\": null}", JsonObject.class));​
rows.add(gson.fromJson("{\"id\": 3, \"vector\": [0.3, 0.4, 0.5, 0.6, 0.7]}", JsonObject.class));​
​
InsertResp insertR = client.insert(InsertReq.builder()​
        .collectionName("user_profiles_null")​
        .data(rows)​
        .build());​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
const data = [​
  { id: 1, vector: [0.1, 0.2, 0.3, 0.4, 0.5], age: 30 },​
  { id: 2, vector: [0.2, 0.3, 0.4, 0.5, 0.6], age: null },​
  { id: 3, vector: [0.3, 0.4, 0.5, 0.6, 0.7] },​
];​
​
client.insert({​
  collection_name: "user_profiles_null",​
  data: data,​
});​
​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
curl --request POST \​
--url "${CLUSTER_ENDPOINT}/v2/vectordb/entities/insert" \​
--header "Authorization: Bearer ${TOKEN}" \​
--header "Content-Type: application/json" \​
-d '{​
    "data": [​
        {"id": 1, "vector": [0.1, 0.2, 0.3, 0.4, 0.5], "age": 30},​
        {"id": 2, "vector": [0.2, 0.3, 0.4, 0.5, 0.6], "age": null}, ​
        {"id": 3, "vector": [0.3, 0.4, 0.5, 0.6, 0.7]} ​
    ],​
    "collectionName": "user_profiles_null"​
}'​

```

</TabItem></Tabs>

<table data-block-token="Gi7ldZKD4oMEkwxw2PYckZetnde"><thead><tr><th data-block-token="WxAEdNXKyou925xUAcVcVaLcnyg" colspan="1" rowspan="1"></th><th data-block-token="FfDidXGW8oiIqOxukilcdUVTndD" colspan="1" rowspan="1"></th><th data-block-token="Dq6yd6eNUovpXIx0r0dcicTwnYd" colspan="1" rowspan="1"></th><th data-block-token="L39udf6xzoJJ3vxhfoZcScDunng" colspan="1" rowspan="1"></th><th data-block-token="SNTsd8G9ro36IXx2JhJcMeIBnIg" colspan="1" rowspan="1"></th><th data-block-token="WKWRdSUvJo4RPqxwQd5c6Yx0nlg" colspan="1" rowspan="1"></th></tr></thead><tbody><tr><td data-block-token="EoSSd886locDBExzMuQcm02WnDh" colspan="1" rowspan="1"></td><td data-block-token="CvghdUnmjo4k4Fxs1GDcaGJBnve" colspan="1" rowspan="1"></td><td data-block-token="XdPBdokpCoegmuxIJZ2cHPWDnig" colspan="1" rowspan="1"></td><td data-block-token="PvT9dOlGSoucOJxquiGcODKnnTf" colspan="1" rowspan="1"></td><td data-block-token="LsmSd2O3CoX7GcxFLStc4TPInBh" colspan="1" rowspan="1"></td><td data-block-token="WUscdtkvuoBBpfx7pWkcNI8lnCL" colspan="1" rowspan="1"></td></tr><tr><td data-block-token="FsG6d3RmromG4LxHlB4cggQunFe" colspan="1" rowspan="1"></td><td data-block-token="GXLido4zLoQbT6xIbzwcTlUtnfF" colspan="1" rowspan="1"></td><td data-block-token="LBDsdBZY1oEofnxSfXLcDLZanqc" colspan="1" rowspan="1"></td><td data-block-token="J6mHdLvORom0DPx88A8cJh04n0e" colspan="1" rowspan="1"></td><td data-block-token="OC7MdTHC2oXSYIx9nyDcEyuvnvN" colspan="1" rowspan="1"></td><td data-block-token="Em58dDgq0oandfx2559cOceKnrf" colspan="1" rowspan="1"></td></tr><tr><td data-block-token="CkxKddn0zoB90WxuYl6cmH8FnYd" colspan="1" rowspan="1"></td><td data-block-token="VXvodfNN4o5OO6xjU9KcsBD0nmh" colspan="1" rowspan="1"></td><td data-block-token="GKxFdH8DWotA00xs2OxcLc3anZb" colspan="1" rowspan="1"></td><td data-block-token="LMoOdsCMgoR0t2x100mc4VGYnKc" colspan="1" rowspan="1"></td><td data-block-token="MhkgdlwF8oqe0PxwWWUc7ITunRd" colspan="1" rowspan="1"></td><td data-block-token="Z4Pwd3jyAoUSInxMcBOcUWEKnsh" colspan="1" rowspan="1"></td></tr><tr><td data-block-token="Lttqdq5x9oh9y3xNpeRc4r7znNb" colspan="1" rowspan="1"></td><td data-block-token="LraFdTuPEoYEL7xvvp7c7HohnSg" colspan="1" rowspan="1"></td><td data-block-token="My2odlLHrozxTsxew50cG8zNnQd" colspan="1" rowspan="1"></td><td data-block-token="JzfEdI3Bgo6bdKx3PnRcBsaLnpg" colspan="1" rowspan="1"></td><td data-block-token="SR3ldJQyMo7FBgxOz6Gc8geCnDc" colspan="1" rowspan="1"></td><td data-block-token="CXtrdMxZ3ok805xUT3UcIAImnlg" colspan="1" rowspan="1"></td></tr><tr><td data-block-token="Pdwzdlulmo3KXyxVyCxcA6hUnoh" colspan="1" rowspan="1"></td><td data-block-token="Y1GfdA34to3471xvc9ScpW3rnDf" colspan="1" rowspan="1"></td><td data-block-token="LyHDd5nURoFwDjxQk6nco6bKn2c" colspan="1" rowspan="1"></td><td data-block-token="RfKodg3PxoPhbSxgW3Sc98YFnHd" colspan="1" rowspan="1"></td><td data-block-token="BiFSdz8duorkyBxAlONcO7gunFh" colspan="1" rowspan="1"></td><td data-block-token="UuuDd1LQeoReyNxiM3Jcz1NFnmb" colspan="1" rowspan="1"></td></tr></tbody></table>

### Search and query with null values​{#search-and-query-with-null-values​}

When using the `search` method, if a field contains `null` values, the search result will return the field as null:​

<Tabs><TabItem value="Python" label="python" default>

```Python
res = client.search(​
    collection_name="user_profiles_null",​
    data=[[0.1, 0.2, 0.4, 0.3, 0.128]],​
    limit=2,​
    search_params={"params": {"nprobe": 16}},​
    output_fields=["id", "age"]​
)​
​
print(res)​
​
# Output​
# data: ["[{'id': 1, 'distance': 0.15838398039340973, 'entity': {'age': 30, 'id': 1}}, {'id': 2, 'distance': 0.28278401494026184, 'entity': {'age': None, 'id': 2}}]"] ​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
import io.milvus.v2.service.vector.request.SearchReq;​
import io.milvus.v2.service.vector.request.data.FloatVec;​
import io.milvus.v2.service.vector.response.SearchResp;​
​
Map<String,Object> params = new HashMap<>();​
params.put("nprobe", 16);​
SearchResp resp = client.search(SearchReq.builder()​
        .collectionName("user_profiles_null")​
        .annsField("vector")​
        .data(Collections.singletonList(new FloatVec(new float[]{0.1f, 0.2f, 0.3f, 0.4f, 0.5f})))​
        .topK(2)​
        .searchParams(params)​
        .outputFields(Arrays.asList("id", "age"))​
        .build());​
​
System.out.println(resp.getSearchResults());​
​
// Output​
//​
// [[SearchResp.SearchResult(entity={id=1, age=30}, score=0.0, id=1), SearchResp.SearchResult(entity={id=2, age=null}, score=0.050000004, id=2)]]​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
client.search({​
    collection_name: 'user_profiles_null',​
    data: [0.3, -0.6, 0.1, 0.3, 0.5],​
    limit: 2,​
    output_fields: ['age', 'id'],​
    filter: '25 <= age <= 35',​
    params: {​
        nprobe: 16​
    }​
});​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
curl --request POST \​
--url "${CLUSTER_ENDPOINT}/v2/vectordb/entities/search" \​
--header "Authorization: Bearer ${TOKEN}" \​
--header "Content-Type: application/json" \​
-d '{​
    "collectionName": "user_profiles_null",​
    "data": [​
        [0.1, -0.2, 0.3, 0.4, 0.5]​
    ],​
    "annsField": "vector",​
    "limit": 5,​
    "outputFields": ["id", "age"]​
}'​
​
#{"code":0,"cost":0,"data":[{"age":30,"distance":0.16000001,"id":1},{"age":null,"distance":0.28999996,"id":2},{"age":null,"distance":0.52000004,"id":3}]}​

```

</TabItem></Tabs>

When you use the `query` method for scalar filtering, the filtering results for null values are all false, indicating that they will not be selected.​

<Tabs><TabItem value="Python" label="python" default>

```Python
# Reviewing previously inserted data:​
# {"id": 1, "vector": [0.1, 0.2, ..., 0.128], "age": 30}​
# {"id": 2, "vector": [0.2, 0.3, ..., 0.129], "age": None}​
# {"id": 3, "vector": [0.3, 0.4, ..., 0.130], "age": None}  # Omitted age  column is treated as None​
​
results = client.query(​
    collection_name="user_profiles_null",​
    filter="age >= 0",​
    output_fields=["id", "age"]​
)​
​
# Example output:​
# [​
#     {"id": 1, "age": 30}​
# ]​
# Note: Entities with `age` as `null` (id 2 and 3) will not appear in the result.​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
import io.milvus.v2.service.vector.request.QueryReq;​
import io.milvus.v2.service.vector.response.QueryResp;​
​
QueryResp resp = client.query(QueryReq.builder()​
        .collectionName("user_profiles_null")​
        .filter("age >= 0")​
        .outputFields(Arrays.asList("id", "age"))​
        .build());​
​
System.out.println(resp.getQueryResults());​
​
// Output​
//​
// [QueryResp.QueryResult(entity={id=1, age=30})]​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
const results = await client.query(​
    collection_name: "user_profiles_null",​
    filter: "age >= 0",​
    output_fields: ["id", "age"]​
);​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
curl --request POST \​
--url "${CLUSTER_ENDPOINT}/v2/vectordb/entities/query" \​
--header "Authorization: Bearer ${TOKEN}" \​
--header "Content-Type: application/json" \​
-d '{​
    "collectionName": "user_profiles_null",​
    "filter": "age >= 0",​
    "outputFields": ["id", "age"]​
}'​
​
# {"code":0,"cost":0,"data":[{"age":30,"id":1}]}​

```

</TabItem></Tabs>

To query entities with `null` values, use an empty expression `""`:​

<Tabs><TabItem value="Python" label="python" default>

```Python
null_results = client.query(​
    collection_name="user_profiles_null",​
    filter="",​
    output_fields=["id", "age"]​
)​
​
# Example output:​
# [{"id": 2, "age": None}, {"id": 3, "age": None}]​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
QueryResp resp = client.query(QueryReq.builder()​
        .collectionName("user_profiles_null")​
        .filter("")​
        .outputFields(Arrays.asList("id", "age"))​
        .limit(10)​
        .build());​
​
System.out.println(resp.getQueryResults());​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
const results = await client.query(​
    collection_name: "user_profiles_null",​
    filter: "",​
    output_fields: ["id", "age"]​
);​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
curl --request POST \​
--url "${CLUSTER_ENDPOINT}/v2/vectordb/entities/query" \​
--header "Authorization: Bearer ${TOKEN}" \​
--header "Content-Type: application/json" \​
-d '{​
    "collectionName": "user_profiles_null",​
    "expr": "",​
    "outputFields": ["id", "age"]​
}'​
​
# {"code":0,"cost":0,"data":[{"age":30,"id":1},{"age":null,"id":2},{"age":null,"id":3}]}​

```

</TabItem></Tabs>

## Default values​{#default-values​}

Default values are preset values assigned to scalar fields. If you do not provide a value for a field with a default during insertion, the system automatically uses the default value.​

### Set default values​{#set-default-values​}

When creating a collection, use the `default_value` parameter to define the default value for a field. The following example shows how to set the default value of `age` to `18` and `status` to `"active"`:​

<Tabs><TabItem value="Python" label="python" default>

```Python
schema = client.create_schema(​
    auto_id=False,​
    enable_dynamic_schema=True,​
)​
​
schema.add_field(field_name="id", datatype=DataType.INT64, is_primary=True)​
schema.add_field(field_name="vector", datatype=DataType.FLOAT_VECTOR, dim=5)​
schema.add_field(field_name="age", datatype=DataType.INT64, default_value=18)​
schema.add_field(field_name="status", datatype=DataType.VARCHAR, default_value="active", max_length=10)​
​
index_params = client.prepare_index_params()​
index_params.add_index(field_name="vector", index_type="IVF_FLAT", metric_type="L2", params={ "nlist": 128 })​
​
client.create_collection(collection_name="user_profiles_default", schema=schema, index_params=index_params)​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
import io.milvus.v2.common.DataType;​
import io.milvus.v2.common.IndexParam;​
import io.milvus.v2.service.collection.request.AddFieldReq;​
import io.milvus.v2.service.collection.request.CreateCollectionReq;​
​
import java.util.*;​
​
CreateCollectionReq.CollectionSchema schema = client.createSchema();​
schema.setEnableDynamicField(true);​
​
schema.addField(AddFieldReq.builder()​
        .fieldName("id")​
        .dataType(DataType.Int64)​
        .isPrimaryKey(true)​
        .build());​
​
schema.addField(AddFieldReq.builder()​
        .fieldName("vector")​
        .dataType(DataType.FloatVector)​
        .dimension(5)​
        .build());​
​
schema.addField(AddFieldReq.builder()​
        .fieldName("age")​
        .dataType(DataType.Int64)​
        .defaultValue(18L)​
        .build());​
​
schema.addField(AddFieldReq.builder()​
        .fieldName("status")​
        .dataType(DataType.VarChar)​
        .maxLength(10)​
        .defaultValue("active")​
        .build());​
​
List<IndexParam> indexes = new ArrayList<>();​
Map<String,Object> extraParams = new HashMap<>();​
extraParams.put("nlist", 128);​
indexes.add(IndexParam.builder()​
        .fieldName("vector")​
        .indexType(IndexParam.IndexType.IVF_FLAT)​
        .metricType(IndexParam.MetricType.L2)​
        .extraParams(extraParams)​
        .build());​
​
CreateCollectionReq requestCreate = CreateCollectionReq.builder()​
        .collectionName("user_profiles_default")​
        .collectionSchema(schema)​
        .indexParams(indexes)​
        .build();​
client.createCollection(requestCreate);​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
import { MilvusClient, DataType } from "@zilliz/milvus2-sdk-node";​
​
const client = new MilvusClient({​
  address: "http://localhost:19530",​
  token: "root:Milvus",​
});​
​
await client.createCollection({​
  collection_name: "user_profiles_default",​
  schema: [​
    {​
      name: "id",​
      is_primary_key: true,​
      data_type: DataType.int64,​
    },​
    { name: "vector", data_type: DataType.FloatVector, dim: 5 },​
    { name: "age", data_type: DataType.Int64, default_value: 18 },​
    { name: 'status', data_type: DataType.VarChar, max_length: 30, default_value: 'active'},​
  ],​
​
  index_params: [​
    {​
      index_name: "vector_inde",​
      field_name: "vector",​
      metric_type: MetricType.L2,​
      index_type: IndexType.IVF_FLAT,​
    },​
  ],​
});​
​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
export pkField='{​
    "fieldName": "id",​
    "dataType": "Int64",​
    "isPrimary": true​
}'​
​
export vectorField='{​
    "fieldName": "vector",​
    "dataType": "FloatVector",​
    "elementTypeParams": {​
        "dim": 5​
    }​
}'​
​
export defaultValueField1='{​
    "fieldName": "age",​
    "dataType": "Int64",​
    "defaultValue": 18​
}'​
​
export defaultValueField2='{​
    "fieldName": "status",​
    "dataType": "VarChar",​
    "defaultValue": "active",​
    "elementTypeParams": {​
        "max_length": 10​
    }​
}'​
​
export schema="{​
    \"autoID\": false,​
    \"fields\": [​
        $pkField,​
        $vectorField,​
        $defaultValueField1,​
        $defaultValueField2​
    ]​
}"​
​
export indexParams='[​
        {​
            "fieldName": "vector",​
            "metricType": "L2",​
            "indexType": "IVF_FLAT",​
            "params":{"nlist": 128}​
        }​
    ]'​
​
curl --request POST \​
--url "${CLUSTER_ENDPOINT}/v2/vectordb/collections/create" \​
--header "Authorization: Bearer ${TOKEN}" \​
--header "Content-Type: application/json" \​
-d "{​
    \"collectionName\": \"user_profiles_default\",​
    \"schema\": $schema,​
    \"indexParams\": $indexParams​
}"​

```

</TabItem></Tabs>

### Insert entities​{#insert-entities​}

When inserting data, if you omit fields with a default value or set their value to null, the system uses the default value:​

<Tabs><TabItem value="Python" label="python" default>

```Python
data = [​
    {"id": 1, "vector": [0.1, 0.2, ..., 0.128], "age": 30, "status": "premium"},​
    {"id": 2, "vector": [0.2, 0.3, ..., 0.129]},  # `age` and `status` use default values​
    {"id": 3, "vector": [0.3, 0.4, ..., 0.130], "age": 25, "status": None},  # `status` uses default value​
    {"id": 4, "vector": [0.4, 0.5, ..., 0.131], "age": None, "status": "inactive"}  # `age` uses default value​
]​
​
client.insert(collection_name="user_profiles_default", data=data)​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
import com.google.gson.Gson;​
import com.google.gson.JsonObject;​
​
import io.milvus.v2.service.vector.request.InsertReq;​
import io.milvus.v2.service.vector.response.InsertResp;​
​
List<JsonObject> rows = new ArrayList<>();​
Gson gson = new Gson();​
rows.add(gson.fromJson("{\"id\": 1, \"vector\": [0.1, 0.2, 0.3, 0.4, 0.5], \"age\": 30, \"status\": \"premium\"}", JsonObject.class));​
rows.add(gson.fromJson("{\"id\": 2, \"vector\": [0.2, 0.3, 0.4, 0.5, 0.6]}", JsonObject.class));​
rows.add(gson.fromJson("{\"id\": 3, \"vector\": [0.3, 0.4, 0.5, 0.6, 0.7], \"age\": 25, \"status\": null}", JsonObject.class));​
rows.add(gson.fromJson("{\"id\": 4, \"vector\": [0.4, 0.5, 0.6, 0.7, 0.8], \"age\": null, \"status\": \"inactive\"}", JsonObject.class));​
​
InsertResp insertR = client.insert(InsertReq.builder()​
        .collectionName("user_profiles_default")​
        .data(rows)​
        .build());​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
const data = [​
    {"id": 1, "vector": [0.1, 0.2, 0.3, 0.4, 0.5], "age": 30, "status": "premium"},​
    {"id": 2, "vector": [0.2, 0.3, 0.4, 0.5, 0.6]}, ​
    {"id": 3, "vector": [0.3, 0.4, 0.5, 0.6, 0.7], "age": 25, "status": null}, ​
    {"id": 4, "vector": [0.4, 0.5, 0.6, 0.7, 0.8], "age": null, "status": "inactive"}  ​
];​
​
client.insert({​
  collection_name: "user_profiles_default",​
  data: data,​
});​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
curl --request POST \​
--url "${CLUSTER_ENDPOINT}/v2/vectordb/entities/insert" \​
--header "Authorization: Bearer ${TOKEN}" \​
--header "Content-Type: application/json" \​
-d '{​
    "data": [​
        {"id": 1, "vector": [0.1, 0.2, 0.3, 0.4, 0.5], "age": 30, "status": "premium"},​
        {"id": 2, "vector": [0.2, 0.3, 0.4, 0.5, 0.6]},​
        {"id": 3, "vector": [0.3, 0.4, 0.5, 0.6, 0.7], "age": 25, "status": null}, ​
        {"id": 4, "vector": [0.4, 0.5, 0.6, 0.7, 0.8], "age": null, "status": "inactive"}      ​
    ],​
    "collectionName": "user_profiles_default"​
}'​

```

</TabItem></Tabs>

:::info[📘 Notes​]

For more information on how nullable and default value settings take effect, refer to [Applicable rules](https://zilliverse.feishu.cn/wiki/PdzYwMm4xidq0hkntA3cDLmcn70?renamingWikiNode=true#share-XD5ldz58eo0QmXx31cec8VQZn4g). ​

:::

### Search and query with default values​{#search-and-query-with-default-values​}

Entities that contain default values are treated the same as any other entities during vector searches and scalar filtering. You can include default values as part of your `search` and `query` operations.​

For example, in a `search` operation, entities with `age` set to the default value of `18` will be included in the results:​

<Tabs><TabItem value="Python" label="python" default>

```Python
res = client.search(​
    collection_name="user_profiles_default",​
    data=[[0.1, 0.2, 0.4, 0.3, 0.128]],​
    search_params={"params": {"nprobe": 16}},​
    filter="age == 18",  # 18 is the default value of the `age` field​
    limit=10,​
    output_fields=["id", "age", "status"]​
)​
​
print(res)​
​
# Output​
# data: ["[{'id': 2, 'distance': 0.28278401494026184, 'entity': {'id': 2, 'age': 18, 'status': 'active'}}, {'id': 4, 'distance': 0.8315839767456055, 'entity': {'id': 4, 'age': 18, 'status': 'inactive'}}]"] ​
​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
import io.milvus.v2.service.vector.request.SearchReq;​
import io.milvus.v2.service.vector.request.data.FloatVec;​
import io.milvus.v2.service.vector.response.SearchResp;​
​
Map<String,Object> params = new HashMap<>();​
params.put("nprobe", 16);​
SearchResp resp = client.search(SearchReq.builder()​
        .collectionName("user_profiles_default")​
        .annsField("vector")​
        .data(Collections.singletonList(new FloatVec(new float[]{0.1f, 0.2f, 0.3f, 0.4f, 0.5f})))​
        .searchParams(params)​
        .filter("age == 18")​
        .topK(10)​
        .outputFields(Arrays.asList("id", "age", "status"))​
        .build());​
​
System.out.println(resp.getSearchResults());​
​
// Output​
//​
// [[SearchResp.SearchResult(entity={id=2, age=18, status=active}, score=0.050000004, id=2), SearchResp.SearchResult(entity={id=4, age=18, status=inactive}, score=0.45000002, id=4)]]​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
client.search({​
    collection_name: 'user_profiles_default',​
    data: [0.3, -0.6, 0.1, 0.3, 0.5],​
    limit: 2,​
    output_fields: ['age', 'id', 'status'],​
    filter: 'age == 18',​
    params: {​
        nprobe: 16​
    }​
});​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
curl --request POST \​
--url "${CLUSTER_ENDPOINT}/v2/vectordb/entities/search" \​
--header "Authorization: Bearer ${TOKEN}" \​
--header "Content-Type: application/json" \​
-d '{​
    "collectionName": "user_profiles_default",​
    "data": [​
        [0.1, 0.2, 0.3, 0.4, 0.5]​
    ],​
    "annsField": "vector",​
    "limit": 5,​
    "filter": "age == 18",​
    "outputFields": ["id", "age", "status"]​
}'​
​
# {"code":0,"cost":0,"data":[{"age":18,"distance":0.050000004,"id":2,"status":"active"},{"age":18,"distance":0.45000002,"id":4,"status":"inactive"}]}​

```

</TabItem></Tabs>

In a `query` operation, you can match or filter by default values directly:​

<Tabs><TabItem value="Python" label="python" default>

```Python
# Query all entities where `age` equals the default value (18)​
default_age_results = client.query(​
    collection_name="user_profiles_default",​
    filter="age == 18",​
    output_fields=["id", "age", "status"]​
)​
​
# Query all entities where `status` equals the default value ("active")​
default_status_results = client.query(​
    collection_name="user_profiles_default",​
    filter='status == "active"',​
    output_fields=["id", "age", "status"]​
)​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
import io.milvus.v2.service.vector.request.QueryReq;​
import io.milvus.v2.service.vector.response.QueryResp;​
​
QueryResp ageResp = client.query(QueryReq.builder()​
        .collectionName("user_profiles_default")​
        .filter("age == 18")​
        .outputFields(Arrays.asList("id", "age", "status"))​
        .build());​
​
System.out.println(ageResp.getQueryResults());​
​
// Output​
//​
// [QueryResp.QueryResult(entity={id=2, age=18, status=active}), QueryResp.QueryResult(entity={id=4, age=18, status=inactive})]​
​
QueryResp statusResp = client.query(QueryReq.builder()​
        .collectionName("user_profiles_default")​
        .filter("status == \"active\"")​
        .outputFields(Arrays.asList("id", "age", "status"))​
        .build());​
​
System.out.println(statusResp.getQueryResults());​
​
// Output​
//​
// [QueryResp.QueryResult(entity={id=2, age=18, status=active}), QueryResp.QueryResult(entity={id=3, age=25, status=active})]​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
// Query all entities where `age` equals the default value (18)​
const default_age_results = await client.query(​
    collection_name: "user_profiles_default",​
    filter: "age == 18",​
    output_fields: ["id", "age", "status"]​
);​
// Query all entities where `status` equals the default value ("active")​
const default_status_results = await client.query(​
    collection_name: "user_profiles_default",​
    filter: 'status == "active"',​
    output_fields: ["id", "age", "status"]​
)​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
curl --request POST \​
--url "${CLUSTER_ENDPOINT}/v2/vectordb/entities/query" \​
--header "Authorization: Bearer ${TOKEN}" \​
--header "Content-Type: application/json" \​
-d '{​
    "collectionName": "user_profiles_default",​
    "filter": "age == 18",​
    "outputFields": ["id", "age", "status"]​
}'​
​
# {"code":0,"cost":0,"data":[{"age":18,"id":2,"status":"active"},{"age":18,"id":4,"status":"inactive"}]}​
​
curl --request POST \​
--url "${CLUSTER_ENDPOINT}/v2/vectordb/entities/query" \​
--header "Authorization: Bearer ${TOKEN}" \​
--header "Content-Type: application/json" \​
-d '{​
    "collectionName": "user_profiles_default",​
    "filter": "status == \"active\"",​
    "outputFields": ["id", "age", "status"]​
}'​
​
# {"code":0,"cost":0,"data":[{"age":18,"id":2,"status":"active"},{"age":25,"id":3,"status":"active"}]}​

```

</TabItem></Tabs>

## Applicable rules​{#applicable-rules​}

The following table summarizes the behavior of nullable columns and default values under different configuration combinations. These rules determine how Milvus handles data when attempting to insert null values or if field values are not provided.​

​


# Number Field​

Number fields are used to store non-vector numerical data in Milvus. These fields are typically employed to describe additional information related to vector data, such as age, price, etc. By using this data, you can better describe vectors and improve the efficiency of data filtering and conditional queries.​

Number fields are particularly useful in many scenarios. For example, in e-commerce recommendations, a price field can be used for filtering; in user profile analysis, age ranges can help refine the results. Combined with vector data, number fields can help the system provide similarity searches while meeting personalized user needs more precisely.​

## Supported number field types​{#supported-number-field-types​}

Milvus supports various number field types to meet different data storage and query needs:​

<table data-block-token="GN2LddpOsooPNNxlx2Bcv3stnvg"><thead><tr><th data-block-token="GatwdsufBoIrb6xlaoBci8XInud" colspan="1" rowspan="1"><p data-block-token="AFFOdIzt4o0EDbxcwINcw0xenrd">Field Type​</p>

</th><th data-block-token="AGYrd69etohgaUxzUyGcXFw8npI" colspan="1" rowspan="1"><p data-block-token="Qbx1dsbirortMixjxXJcukoLnjR">Description​</p>

</th></tr></thead><tbody><tr><td data-block-token="FQ0rdk7NKoAmtUxD5n7cHWBfnKd" colspan="1" rowspan="1"><p data-block-token="J4YBdReSPol6jvxIPyxcs7lRnGQ"><code>`BOOL`</code>​</p>

</td><td data-block-token="XfVYdowyvoY7iwxNCIBcRbE4nFf" colspan="1" rowspan="1"><p data-block-token="WYGTdKI4RoBTXbxR2YbcxC2InOb">Boolean type for storing <code>`true`</code> or <code>`false`</code>, suitable for describing binary states such as "in stock" or "active."​</p>

</td></tr><tr><td data-block-token="G6JBdjvguofEOnx6lmQcXkJdn6o" colspan="1" rowspan="1"><p data-block-token="PGcDd6i5Ao3jioxzrLkcV5lanUq"><code>`INT8`</code>​</p>

</td><td data-block-token="TEVDdqVe0ooqTbxqkW7cdu8OnMe" colspan="1" rowspan="1"><p data-block-token="G5AOdYaoEom6X0x3NUKc9YL1nRh">8-bit integer, suitable for storing small-range integer data.​</p>

</td></tr><tr><td data-block-token="Zc6cdGRmVoEOzdxaT8Pc4jdmnxg" colspan="1" rowspan="1"><p data-block-token="SaIUd6XDYoo2msxLCSXcNJk5nre"><code>`INT16`</code>​</p>

</td><td data-block-token="EamldyccGovIeKxaLQ4cxmjMng2" colspan="1" rowspan="1"><p data-block-token="Lx9FdawAgoIlZXxGomRcaglPnyc">16-bit integer, for medium-range integer data.​</p>

</td></tr><tr><td data-block-token="SPeCdRoc4owdXXxWSDVcNXwVnVf" colspan="1" rowspan="1"><p data-block-token="AL4sd4HrJokAj2xwglOcxIAcnNc"><code>`INT32`</code>​</p>

</td><td data-block-token="PySwdD4CHot4YgxrOwycN2ngnAb" colspan="1" rowspan="1"><p data-block-token="FYgYdL9PPoNme4xOo62cud2Gnob">32-bit integer, ideal for general integer data storage like product quantities or user IDs.​</p>

</td></tr><tr><td data-block-token="HZWpdo7SuoA04KxvZAxcflidn9c" colspan="1" rowspan="1"><p data-block-token="NbO6dTRRToj5YNxzjICcJe8YnPh"><code>`INT64`</code>​</p>

</td><td data-block-token="FberdUuiZoyA0mxK6T4cfYpqnUf" colspan="1" rowspan="1"><p data-block-token="ZuTHdAIJ5oT8G7xvkJdcGt70nGq">64-bit integer, suitable for storing large-range data like timestamps or identifiers.​</p>

</td></tr><tr><td data-block-token="XWCHd4raooSVtXxKE58cE3j0nwd" colspan="1" rowspan="1"><p data-block-token="NWOCdcYiYoMVZRxknoicMsk5nae"><code>`FLOAT`</code>​</p>

</td><td data-block-token="PqINdhj44oido7xzrTMcQA2OnDh" colspan="1" rowspan="1"><p data-block-token="BA2jdC2afoK4duxqG8lcJln8nLH">32-bit floating-point number, for data requiring general precision, such as ratings or temperature.​</p>

</td></tr><tr><td data-block-token="I3YZdrlQcoGhPExUIq0cQUDDnFe" colspan="1" rowspan="1"><p data-block-token="MKqAdpPoPovAxWxjeAXcF6PmnfK"><code>`DOUBLE`</code>​</p>

</td><td data-block-token="Vb2Cdz3wVoBoizxAwswc9CvFnXf" colspan="1" rowspan="1"><p data-block-token="R501ddb8Uoir53xLFwecx1BenVe">64-bit double-precision floating-point number, for high-precision data like financial information or scientific calculations.​</p>

</td></tr></tbody></table>

## Add number field​{#add-number-field​}

To use number fields in Milvus, define the relevant fields in the collection schema, setting the `datatype` to a supported type such as `BOOL` or `INT8`. For a complete list of supported number field types, refer to [Supported number field types](https://zilliverse.feishu.cn/wiki/EwArwXCOPip15hkSvvpciAMJnSe#share-BbV6dSb4BogF8txp3QCc0KU1n2e).​

The following example shows how to define a schema that includes number fields `age` and `price`:​

<Tabs><TabItem value="Python" label="python" default>

```Python
from pymilvus import MilvusClient, DataType​
​
client = MilvusClient(uri="http://localhost:19530")​
​
schema = client.create_schema(​
    auto_id=False,​
    enable_dynamic_fields=True,​
)​
​
schema.add_field(field_name="age", datatype=DataType.INT64)​
schema.add_field(field_name="price", datatype=DataType.FLOAT)​
schema.add_field(field_name="pk", datatype=DataType.INT64, is_primary=True)​
schema.add_field(field_name="embedding", datatype=DataType.FLOAT_VECTOR, dim=3)​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
import io.milvus.v2.client.ConnectConfig;​
import io.milvus.v2.client.MilvusClientV2;​
​
import io.milvus.v2.common.DataType;​
import io.milvus.v2.service.collection.request.AddFieldReq;​
import io.milvus.v2.service.collection.request.CreateCollectionReq;​
​
​
MilvusClientV2 client = new MilvusClientV2(ConnectConfig.builder()​
        .uri("http://localhost:19530")​
        .build());​
        ​
CreateCollectionReq.CollectionSchema schema = client.createSchema();​
schema.setEnableDynamicField(true);​
​
schema.addField(AddFieldReq.builder()​
        .fieldName("age")​
        .dataType(DataType.Int64)​
        .build());​
​
schema.addField(AddFieldReq.builder()​
        .fieldName("price")​
        .dataType(DataType.Float)​
        .build());​
​
schema.addField(AddFieldReq.builder()​
        .fieldName("pk")​
        .dataType(DataType.Int64)​
        .isPrimaryKey(true)​
        .build());​
​
schema.addField(AddFieldReq.builder()​
        .fieldName("embedding")​
        .dataType(DataType.FloatVector)​
        .dimension(3)​
        .build());​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
import { MilvusClient, DataType } from "@zilliz/milvus2-sdk-node";​
const schema = [​
  {​
    name: "age",​
    data_type: DataType.Int64,​
  },​
  {​
    name: "price",​
    data_type: DataType.Float,​
  },​
  {​
    name: "pk",​
    data_type: DataType.Int64,​
    is_primary_key: true,​
  },​
  {​
    name: "embedding",​
    data_type: DataType.FloatVector,​
    dim: 3,​
  },​
];​
​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
export int64Field='{​
    "fieldName": "age",​
    "dataType": "Int64"​
}'​
​
export floatField='{​
    "fieldName": "price",​
    "dataType": "Float"​
}'​
​
export pkField='{​
    "fieldName": "pk",​
    "dataType": "Int64",​
    "isPrimary": true​
}'​
​
export vectorField='{​
    "fieldName": "embedding",​
    "dataType": "FloatVector",​
    "elementTypeParams": {​
        "dim": 3​
    }​
}'​
​
export schema="{​
    \"autoID\": false,​
    \"fields\": [​
        $int64Field,​
        $floatField,​
        $pkField,​
        $vectorField​
    ]​
}"​

```

</TabItem></Tabs>

:::info[📘 Notes​]

The primary field and vector field are mandatory when you create a collection. The primary field uniquely identifies each entity, while the vector field is crucial for similarity search. For more details, refer to [​Primary Field & AutoID](https://zilliverse.feishu.cn/wiki/D2ctwKZhNilLY0ke1vpcHL62n5G), [​Dense Vector](https://zilliverse.feishu.cn/wiki/ARalwpaVDiCwDZkoSHtcPNgXnRg), [​Binary Vector](https://zilliverse.feishu.cn/wiki/NTwawtvYdiXTkukbss7ccw2RnXc), or [​Sparse Vector](https://zilliverse.feishu.cn/wiki/JbPDwHqd0iZZSuk5tYicGqKbn9c).​

:::

## Set index params​{#set-index-params​}

Setting index parameters for number fields is optional but can significantly improve retrieval efficiency.​

In the following example, we create an `AUTOINDEX` for the `age` number field, allowing Milvus to automatically create an appropriate index based on the data type. For more information, refer to [​AUTOINDEX](https://zilliverse.feishu.cn/wiki/Sp4XwxJ6gi27Vok6B3Ycmsirnkg).​

<Tabs><TabItem value="Python" label="python" default>

```Python
index_params = client.prepare_index_params()​
​
index_params.add_index(​
    field_name="age",​
    index_type="AUTOINDEX",​
    index_name="inverted_index"​
)​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
import io.milvus.v2.common.IndexParam;​
import java.util.*;​
​
List<IndexParam> indexes = new ArrayList<>();​
indexes.add(IndexParam.builder()​
        .fieldName("age")​
        .indexType(IndexParam.IndexType.AUTOINDEX)​
        .build());​
​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
const indexParams = {​
    index_name: 'inverted_index',​
    field_name: 'age',​
    index_type: IndexType.AUTOINDEX,​
);​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
export indexParams='[​
        {​
            "fieldName": "age",​
            "indexName": "inverted_index",​
            "indexType": "AUTOINDEX"​
        }​
    ]'​

```

</TabItem></Tabs>

In addition to `AUTOINDEX`, you can specify other number field index types. For supported index types, refer to [​Scalar Indexes](https://zilliverse.feishu.cn/wiki/PdSnwO3jWiGZHAkW510c5zg0nmm).​

Moreover, before creating the collection, you must create an index for the vector field. In this example, we use `AUTOINDEX` to simplify vector index settings.​

<Tabs><TabItem value="Python" label="python" default>

```Python
# Add vector index​
index_params.add_index(​
    field_name="embedding",​
    index_type="AUTOINDEX",  # Use automatic indexing to simplify complex index settings​
    metric_type="COSINE"  # Specify similarity metric type, options include L2, COSINE, or IP​
)​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
indexes.add(IndexParam.builder()​
        .fieldName("embedding")​
        .indexType(IndexParam.IndexType.AUTOINDEX)​
        .metricType(IndexParam.MetricType.COSINE)​
        .build());​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
import { IndexType } from "@zilliz/milvus2-sdk-node";​
const indexParams = [​
  {​
    field_name: "age",​
    index_name: "inverted_index",​
    index_type: IndexType.AUTOINDEX,​
  },​
  {​
    field_name: "embedding",​
    metric_type: "COSINE",​
    index_type: IndexType.AUTOINDEX,​
  },​
];​
​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
export indexParams='[​
        {​
            "fieldName": "age",​
            "indexName": "inverted_index",​
            "indexType": "AUTOINDEX"​
        },​
        {​
            "fieldName": "embedding",​
            "metricType": "COSINE",​
            "indexType": "AUTOINDEX"​
        }​
    ]'​

```

</TabItem></Tabs>

## Create collection​{#create-collection​}

Once the schema and indexes are defined, you can create a collection that includes number fields.​

<Tabs><TabItem value="Python" label="python" default>

```Python
# Create Collection​
client.create_collection(​
    collection_name="your_collection_name",​
    schema=schema,​
    index_params=index_params​
)​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
CreateCollectionReq requestCreate = CreateCollectionReq.builder()​
        .collectionName("my_scalar_collection")​
        .collectionSchema(schema)​
        .indexParams(indexes)​
        .build();​
client.createCollection(requestCreate);​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
client.create_collection({​
    collection_name: "my_scalar_collection",​
    schema: schema,​
    index_params: indexParams​
})​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
curl --request POST \​
--url "${CLUSTER_ENDPOINT}/v2/vectordb/collections/create" \​
--header "Authorization: Bearer ${TOKEN}" \​
--header "Content-Type: application/json" \​
-d "{​
    \"collectionName\": \"my_scalar_collection\",​
    \"schema\": $schema,​
    \"indexParams\": $indexParams​
}"​

```

</TabItem></Tabs>

## Insert data​{#insert-data​}

After creating the collection, you can insert data that includes number fields.​

<Tabs><TabItem value="Python" label="python" default>

```Python
data = [​
    {"age": 25, "price": 99.99, "pk": 1, "embedding": [0.1, 0.2, 0.3]},​
    {"age": 30, "price": 149.50, "pk": 2, "embedding": [0.4, 0.5, 0.6]},​
    {"age": 35, "price": 199.99, "pk": 3, "embedding": [0.7, 0.8, 0.9]},​
]​
​
client.insert(​
    collection_name="my_scalar_collection",​
    data=data​
)​

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
rows.add(gson.fromJson("{\"age\": 25, \"price\": 99.99, \"pk\": 1, \"embedding\": [0.1, 0.2, 0.3]}", JsonObject.class));​
rows.add(gson.fromJson("{\"age\": 30, \"price\": 149.50, \"pk\": 2, \"embedding\": [0.4, 0.5, 0.6]}", JsonObject.class));​
rows.add(gson.fromJson("{\"age\": 35, \"price\": 199.99, \"pk\": 3, \"embedding\": [0.7, 0.8, 0.9]}", JsonObject.class));​
​
InsertResp insertR = client.insert(InsertReq.builder()​
        .collectionName("my_scalar_collection")​
        .data(rows)​
        .build());​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
const data = [​
  { age: 25, price: 99.99, pk: 1, embedding: [0.1, 0.2, 0.3] },​
  { age: 30, price: 149.5, pk: 2, embedding: [0.4, 0.5, 0.6] },​
  { age: 35, price: 199.99, pk: 3, embedding: [0.7, 0.8, 0.9] },​
];​
​
client.insert({​
  collection_name: "my_scalar_collection",​
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
        {"age": 25, "price": 99.99, "pk": 1, "embedding": [0.1, 0.2, 0.3]},​
        {"age": 30, "price": 149.50, "pk": 2, "embedding": [0.4, 0.5, 0.6]},​
        {"age": 35, "price": 199.99, "pk": 3, "embedding": [0.7, 0.8, 0.9]}       ​
    ],​
    "collectionName": "my_scalar_collection"​
}'​

```

</TabItem></Tabs>

In this example, we insert data that includes `age`, `price`, `pk` (primary field), and vector representations (`embedding`). To ensure that the inserted data matches the fields defined in the schema, it's recommended to check data types in advance to avoid errors.​

If you set `enable_dynamic_fields=True` when defining the schema, Milvus allows you to insert number fields that were not defined in advance. However, keep in mind that this may increase the complexity of queries and management, potentially impacting performance. For more information, refer to [​Dynamic Field](https://zilliverse.feishu.cn/wiki/OVxRwZWxNi4pYrkdKxCcOuY2nf1).​

## Search and query​{#search-and-query​}

After adding number fields, you can use them for filtering in search and query operations to achieve more precise search results.​

### Filter queries​{#filter-queries​}

After adding number fields, you can use them for filtering in queries. For example, you can query all entities where `age` is between 30 and 40:​

<Tabs><TabItem value="Python" label="python" default>

```Python
filter = "30 <= age <= 40"​
​
res = client.query(​
    collection_name="my_scalar_collection",​
    filter=filter,​
    output_fields=["age","price"]​
)​
​
print(res)​
​
# Output​
# data: ["{'age': 30, 'price': np.float32(149.5), 'pk': 2}", "{'age': 35, 'price': np.float32(199.99), 'pk': 3}"] ​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
import io.milvus.v2.service.vector.request.QueryReq;​
import io.milvus.v2.service.vector.response.QueryResp;​
​
String filter = "30 <= age <= 40";​
​
QueryResp resp = client.query(QueryReq.builder()​
        .collectionName("my_scalar_collection")​
        .filter(filter)​
        .outputFields(Arrays.asList("age", "price"))​
        .build());​
System.out.println(resp.getQueryResults());​
​
// Output​
//​
// [QueryResp.QueryResult(entity={price=149.5, pk=2, age=30}), QueryResp.QueryResult(entity={price=199.99, pk=3, age=35})]​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
client.query({​
    collection_name: 'my_scalar_collection',​
    filter: '30 <= age <= 40',​
    output_fields: ['age', 'price']​
});​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
curl --request POST \​
--url "${CLUSTER_ENDPOINT}/v2/vectordb/entities/query" \​
--header "Authorization: Bearer ${TOKEN}" \​
--header "Content-Type: application/json" \​
-d '{​
    "collectionName": "my_scalar_collection",​
    "filter": "30 <= age <= 40",​
    "outputFields": ["age","price"]​
}'​
​
## {"code":0,"cost":0,"data":[{"age":30,"pk":2,"price":149.5},{"age":35,"pk":3,"price":199.99}]}​

```

</TabItem></Tabs>

This query expression returns all matching entities and outputs their `age` and `price` fields. For more information on filter queries, refer to [​Metadata Filtering](https://zilliverse.feishu.cn/wiki/Y3JIwe49Rin8ZiksgoJc11wQnow).​

### Vector search with number filtering​{#vector-search-with-number-filtering​}

In addition to basic number field filtering, you can combine vector similarity searches with number field filters. For example, the following code shows how to add a number field filter to a vector search:​

<Tabs><TabItem value="Python" label="python" default>

```Python
filter = "25 <= age <= 35"​
​
res = client.search(​
    collection_name="my_scalar_collection",​
    data=[[0.3, -0.6, 0.1]],​
    limit=5,​
    search_params={"params": {"nprobe": 10}},​
    output_fields=["age","price"],​
    filter=filter​
)​
​
print(res)​
​
# Output​
# data: ["[{'id': 1, 'distance': -0.06000000238418579, 'entity': {'age': 25, 'price': 99.98999786376953}}, {'id': 2, 'distance': -0.12000000476837158, 'entity': {'age': 30, 'price': 149.5}}, {'id': 3, 'distance': -0.18000000715255737, 'entity': {'age': 35, 'price': 199.99000549316406}}]"]​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
import io.milvus.v2.service.vector.request.SearchReq;​
import io.milvus.v2.service.vector.request.data.FloatVec;​
import io.milvus.v2.service.vector.response.SearchResp;​
​
String filter = "25 <= age <= 35";​
​
SearchResp resp = client.search(SearchReq.builder()​
        .collectionName("my_scalar_collection")​
        .annsField("embedding")​
        .data(Collections.singletonList(new FloatVec(new float[]{0.3f, -0.6f, 0.1f})))​
        .topK(5)​
        .outputFields(Arrays.asList("age", "price"))​
        .filter(filter)​
        .build());​
​
System.out.println(resp.getSearchResults());​
​
// Output​
//​
// [[SearchResp.SearchResult(entity={price=199.99, age=35}, score=-0.19054288, id=3), SearchResp.SearchResult(entity={price=149.5, age=30}, score=-0.20163085, id=2), SearchResp.SearchResult(entity={price=99.99, age=25}, score=-0.2364331, id=1)]]​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
client.search({​
    collection_name: 'my_scalar_collection',​
    data: [0.3, -0.6, 0.1],​
    limit: 5,​
    output_fields: ['age', 'price'],​
    filter: '25 <= age <= 35'​
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
    "collectionName": "my_scalar_collection",​
    "data": [​
        [0.3, -0.6, 0.1]​
    ],​
    "annsField": "embedding",​
    "limit": 5,​
    "outputFields": ["age", "price"]​
}'​
​
## {"code":0,"cost":0,"data":[{"age":35,"distance":-0.19054288,"id":3,"price":199.99},{"age":30,"distance":-0.20163085,"id":2,"price":149.5},{"age":25,"distance":-0.2364331,"id":1,"price":99.99}]}​

```

</TabItem></Tabs>

In this example, we first define a query vector and add a filter condition `25 <= age <= 35` during the search. This ensures that the search results are not only similar to the query vector but also meet the specified age range. For more information, refer to [​Metadata Filtering](https://zilliverse.feishu.cn/wiki/Y3JIwe49Rin8ZiksgoJc11wQnow).​


---
id: use-json-fields.md
title: Use JSON Fields
---

# JSON Field​

[JSON](https://en.wikipedia.org/wiki/JSON) (JavaScript Object Notation) is a lightweight data exchange format that provides a flexible way to store and query complex data structures. In Milvus, you can store additional structured information alongside vector data using JSON fields, enabling advanced searches and queries that combine vector similarity with structured filtering.​

JSON fields are ideal for applications that require metadata to optimize retrieval results. For example, in e-commerce, product vectors can be enhanced with attributes like category, price, and brand. In recommendation systems, user vectors can be combined with preferences and demographic information. Below is an example of a typical JSON field:​

```Python
{​
  "category": "electronics",​
  "price": 99.99,​
  "brand": "BrandA"​
}​

```

## Add JSON field​{#add-json-field​}

To use JSON fields in Milvus, define the relevant field type in the collection schema, setting the `datatype` to the supported JSON type, i.e., `JSON`.​

Here’s how to define a collection schema that includes a JSON field:​

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
schema.add_field(field_name="metadata", datatype=DataType.JSON)​
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
MilvusClientV2 client = new MilvusClientV2(ConnectConfig.builder()​
        .uri("http://localhost:19530")​
        .build());​
        ​
CreateCollectionReq.CollectionSchema schema = client.createSchema();​
schema.setEnableDynamicField(true);​
​
schema.addField(AddFieldReq.builder()​
        .fieldName("metadata")​
        .dataType(DataType.JSON)​
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
    name: "metadata",​
    data_type: DataType.JSON,​
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

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
export jsonField='{​
    "fieldName": "metadata",​
    "dataType": "JSON"​
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
        $jsonField,​
        $pkField,​
        $vectorField​
    ]​
}"​

```

</TabItem></Tabs>

In this example, we add a JSON field called `metadata` to store additional metadata related to vector data, such as product category, price, and brand information.​

:::info[📘 Notes​]

The primary field and vector field are mandatory when you create a collection. The primary field uniquely identifies each entity, while the vector field is crucial for similarity search. For more details, refer to [​Primary Field & AutoID](https://zilliverse.feishu.cn/wiki/D2ctwKZhNilLY0ke1vpcHL62n5G), [​Dense Vector](https://zilliverse.feishu.cn/wiki/ARalwpaVDiCwDZkoSHtcPNgXnRg), [​Binary Vector](https://zilliverse.feishu.cn/wiki/NTwawtvYdiXTkukbss7ccw2RnXc), or [​Sparse Vector](https://zilliverse.feishu.cn/wiki/JbPDwHqd0iZZSuk5tYicGqKbn9c).​

:::

## Create collection​{#create-collection​}

When creating a collection, you must create an index for the vector field to ensure retrieval performance. In this example, we use `AUTOINDEX` to simplify index setup. For more details, refer to [​AUTOINDEX](https://zilliverse.feishu.cn/wiki/Sp4XwxJ6gi27Vok6B3Ycmsirnkg).​

<Tabs><TabItem value="Python" label="python" default>

```Python
​
index_params = client.prepare_index_params()​
​
index_params.add_index(​
    field_name="embedding",​
    index_type="AUTOINDEX",​
    metric_type="COSINE"​
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
        .fieldName("embedding")​
        .indexType(IndexParam.IndexType.AUTOINDEX)​
        .metricType(IndexParam.MetricType.COSINE)​
        .build());​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
const indexParams = {​
    index_name: 'embedding_index',​
    field_name: 'embedding',​
    metricType: MetricType.CONSINE,​
    index_type: IndexType.AUTOINDEX,​
);​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
export indexParams='[​
        {​
            "fieldName": "embedding",​
            "metricType": "COSINE",​
            "indexType": "AUTOINDEX"​
        }​
    ]'​

```

</TabItem></Tabs>

Use the defined schema and index parameters to create a collection:​

<Tabs><TabItem value="Python" label="python" default>

```Python
client.create_collection(​
    collection_name="my_json_collection",​
    schema=schema,​
    index_params=index_params​
)​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
CreateCollectionReq requestCreate = CreateCollectionReq.builder()​
        .collectionName("my_json_collection")​
        .collectionSchema(schema)​
        .indexParams(indexes)​
        .build();​
client.createCollection(requestCreate);​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
client.create_collection({​
    collection_name: "my_json_collection",​
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
    \"collectionName\": \"my_json_collection\",​
    \"schema\": $schema,​
    \"indexParams\": $indexParams​
}"​

```

</TabItem></Tabs>

## Insert data​{#insert-data​}

After creating the collection, you can insert data that includes JSON fields.​

<Tabs><TabItem value="Python" label="python" default>

```Python
# Data to be inserted​
data = [​
  {​
      "metadata": {"category": "electronics", "price": 99.99, "brand": "BrandA"},​
      "pk": 1,​
      "embedding": [0.12, 0.34, 0.56]​
  },​
  {​
      "metadata": {"category": "home_appliances", "price": 249.99, "brand": "BrandB"},​
      "pk": 2,​
      "embedding": [0.56, 0.78, 0.90]​
  },​
  {​
      "metadata": {"category": "furniture", "price": 399.99, "brand": "BrandC"},​
      "pk": 3,​
      "embedding": [0.91, 0.18, 0.23]​
  }​
]​
​
# Insert data into the collection​
client.insert(​
    collection_name="your_collection_name",​
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
rows.add(gson.fromJson("{\"metadata\": {\"category\": \"electronics\", \"price\": 99.99, \"brand\": \"BrandA\"}, \"pk\": 1, \"embedding\": [0.1, 0.2, 0.3]}", JsonObject.class));​
rows.add(gson.fromJson("{\"metadata\": {\"category\": \"home_appliances\", \"price\": 249.99, \"brand\": \"BrandB\"}, \"pk\": 2, \"embedding\": [0.4, 0.5, 0.6]}", JsonObject.class));​
rows.add(gson.fromJson("{\"metadata\": {\"category\": \"furniture\", \"price\": 399.99, \"brand\": \"BrandC\"}, \"pk\": 3, \"embedding\": [0.7, 0.8, 0.9]}", JsonObject.class));​
​
InsertResp insertR = client.insert(InsertReq.builder()​
        .collectionName("my_json_collection")​
        .data(rows)​
        .build());​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
const data = [​
  {​
      "metadata": {"category": "electronics", "price": 99.99, "brand": "BrandA"},​
      "pk": 1,​
      "embedding": [0.12, 0.34, 0.56]​
  },​
  {​
      "metadata": {"category": "home_appliances", "price": 249.99, "brand": "BrandB"},​
      "pk": 2,​
      "embedding": [0.56, 0.78, 0.90]​
  },​
  {​
      "metadata": {"category": "furniture", "price": 399.99, "brand": "BrandC"},​
      "pk": 3,​
      "embedding": [0.91, 0.18, 0.23]​
  }​
]​
​
client.insert({​
    collection_name: "my_json_collection",​
    data: data​
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
        {​
            "metadata": {"category": "electronics", "price": 99.99, "brand": "BrandA"},​
            "pk": 1,​
            "embedding": [0.12, 0.34, 0.56]​
        },​
        {​
            "metadata": {"category": "home_appliances", "price": 249.99, "brand": "BrandB"},​
            "pk": 2,​
            "embedding": [0.56, 0.78, 0.90]​
        },​
        {​
            "metadata": {"category": "furniture", "price": 399.99, "brand": "BrandC"},​
            "pk": 3,​
            "embedding": [0.91, 0.18, 0.23]​
        }       ​
    ],​
    "collectionName": "my_json_collection"​
}'​

```

</TabItem></Tabs>

In this example:​

- Each data entry includes a primary field (`pk`), `metadata` as a JSON field to store information such as product category, price, and brand.​

- `embedding` is a 3-dimensional vector field used for vector similarity search.​

## Search and query​{#search-and-query​}

JSON fields allow scalar filtering during searches, enhancing Milvus's vector search capabilities. You can query based on JSON properties alongside vector similarity.​

### Filter queries​{#filter-queries​}

You can filter data based on JSON properties, such as matching specific values or checking if a number falls within a certain range.​

<Tabs><TabItem value="Python" label="python" default>

```Python
filter = 'metadata["category"] == "electronics" and metadata["price"] < 150'​
​
res = client.query(​
    collection_name="my_json_collection",​
    filter=filter,​
    output_fields=["metadata"]​
)​
​
print(res)​
​
# Output​
# data: ["{'metadata': {'category': 'electronics', 'price': 99.99, 'brand': 'BrandA'}, 'pk': 1}"] ​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
import io.milvus.v2.service.vector.request.QueryReq;​
import io.milvus.v2.service.vector.response.QueryResp;​
​
String filter = "metadata[\"category\"] == \"electronics\" and metadata[\"price\"] < 150";​
QueryResp resp = client.query(QueryReq.builder()​
        .collectionName("my_json_collection")​
        .filter(filter)​
        .outputFields(Collections.singletonList("metadata"))​
        .build());​
​
System.out.println(resp.getQueryResults());​
​
// Output​
//​
// [QueryResp.QueryResult(entity={metadata={"category":"electronics","price":99.99,"brand":"BrandA"}, pk=1})]​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
client.query({​
    collection_name: 'my_scalar_collection',​
    filter: 'metadata["category"] == "electronics" and metadata["price"] < 150',​
    output_fields: ['metadata']​
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
    "collectionName": "my_json_collection",​
    "filter": "metadata[\"category\"] == \"electronics\" and metadata[\"price\"] < 150",​
    "outputFields": ["metadata"]​
}'​
{"code":0,"cost":0,"data":[{"metadata":"{\"category\": \"electronics\", \"price\": 99.99, \"brand\": \"BrandA\"}","pk":1}]}​

```

</TabItem></Tabs>

In the above query, Milvus filters out entities where the `metadata` field has a category of `"electronics"` and a price below 150, returning entities that match these criteria.​

### Vector search with JSON filtering​{#vector-search-with-json-filtering​}

By combining vector similarity with JSON filtering, you can ensure that the retrieved data not only matches semantically but also meets specific business conditions, making the search results more precise and aligned with user needs.​

<Tabs><TabItem value="Python" label="python" default>

```Python
filter = 'metadata["brand"] == "BrandA"'​
​
res = client.search(​
    collection_name="my_json_collection",​
    data=[[0.3, -0.6, 0.1]],​
    limit=5,​
    search_params={"params": {"nprobe": 10}},​
    output_fields=["metadata"],​
    filter=filter​
)​
​
print(res)​
​
# Output​
# data: ["[{'id': 1, 'distance': -0.2479381263256073, 'entity': {'metadata': {'category': 'electronics', 'price': 99.99, 'brand': 'BrandA'}}}]"] ​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
import io.milvus.v2.service.vector.request.SearchReq;​
import io.milvus.v2.service.vector.response.SearchResp;​
​
String filter = "metadata[\"brand\"] == \"BrandA\"";​
SearchResp resp = client.search(SearchReq.builder()​
        .collectionName("my_json_collection")​
        .annsField("embedding")​
        .data(Collections.singletonList(new FloatVec(new float[]{0.3f, -0.6f, 0.1f})))​
        .topK(5)​
        .outputFields(Collections.singletonList("metadata"))​
        .filter(filter)​
        .build());​
​
System.out.println(resp.getSearchResults());​
​
// Output​
//​
// [[SearchResp.SearchResult(entity={metadata={"category":"electronics","price":99.99,"brand":"BrandA"}}, score=-0.2364331, id=1)]]​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
client.search({​
    collection_name: 'my_json_collection',​
    data: [0.3, -0.6, 0.1],​
    limit: 5,​
    output_fields: ['metadata'],​
    filter: 'metadata["category"] == "electronics" and metadata["price"] < 150',​
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
    "collectionName": "my_json_collection",​
    "data": [​
        [0.3, -0.6, 0.1]​
    ],​
    "annsField": "embedding",​
    "limit": 5,​
    "searchParams":{​
        "params":{"nprobe":10}​
    },​
    "outputFields": ["metadata"],​
    "filter": "metadata[\"brand\"] == \"BrandA\""​
}'​
​
## {"code":0,"cost":0,"data":[{"distance":-0.24793813,"id":1,"metadata":"{\"category\": \"electronics\", \"price\": 99.99, \"brand\": \"BrandA\"}"}]}​

```

</TabItem></Tabs>

In this example, Milvus returns the top 5 entities most similar to the query vector, with the `metadata` field containing a brand of `"BrandA"`.​

Additionally, Milvus supports advanced JSON filtering operators such as `JSON_CONTAINS`, `JSON_CONTAINS_ALL`, and `JSON_CONTAINS_ANY`, which can further enhance query capabilities. For more details, refer to [​Metadata Filtering](https://zilliverse.feishu.cn/wiki/Y3JIwe49Rin8ZiksgoJc11wQnow).​

## Limits​{#limits​}

- **Indexing Limitations**: Due to the complexity of data structures, indexing JSON fields is not supported.​

- **Data Type Matching**: If a JSON field's key value is an integer or floating point, it can only be compared with another integer or float key or `INT32/64` or `FLOAT32/64` fields. If the key value is a string (`VARCHAR`), it can only be compared with another string key.​

- **Naming Restrictions**: When naming JSON keys, it is recommended to use only letters, numeric characters, and underscores, as other characters may cause issues during filtering or searching.​

- **Handling String Values**: For string values (`VARCHAR`), Milvus stores JSON field strings as-is without semantic conversion. For example: `'a"b'`, `"a'b"`, `'a\\'b'`, and `"a\\"b"` are stored as entered; however, `'a'b'` and `"a"b"` are considered invalid.​

- **Handling Nested Dictionaries**: Any nested dictionaries within JSON field values are treated as strings.​



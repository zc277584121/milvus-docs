# Sparse Vector​

Sparse vectors are an important method of data representation in information retrieval and natural language processing. While dense vectors are popular for their excellent semantic understanding capabilities, sparse vectors often provide more accurate results when it comes to applications that require precise matching of keywords or phrases.​

## Overview​{#overview​}

A sparse vector is a special representation of high-dimensional vectors where most elements are zero, and only a few dimensions have non-zero values. This characteristic makes sparse vectors particularly effective in handling large-scale, high-dimensional, but sparse data. Common applications include:​

- **Text Analysis:** Representing documents as bag-of-words vectors, where each dimension corresponds to a word, and only words that appear in the document have non-zero values.​

- **Recommendation Systems:** User-item interaction matrices, where each dimension represents a user's rating for a particular item, with most users interacting with only a few items.​

- **Image Processing:** Local feature representation, focusing only on key points in the image, resulting in high-dimensional sparse vectors.​

As shown in the diagram below, dense vectors are typically represented as continuous arrays where each position has a value (e.g., `[0.3, 0.8, 0.2, 0.3, 0.1]`). In contrast, sparse vectors store only non-zero elements and their indices, often represented as key-value pairs (e.g., `[{2: 0.2}, ..., {9997: 0.5}, {9999: 0.7}]`). This representation significantly reduces storage space and increases computational efficiency, especially when dealing with extremely high-dimensional data (e.g., 10,000 dimensions).​

![RSyvdcRVLoAUT2xNaThcwV6ynJg](请手动下载图片并替换)

Sparse vectors can be generated using various methods, such as [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) (Term Frequency-Inverse Document Frequency) and [BM25](https://en.wikipedia.org/wiki/Okapi_BM25) in text processing. Additionally, Milvus offers convenient methods to help generate and process sparse vectors. For details, refer to <ins>Embeddings</ins>.​

For text data, Milvus also provides full-text search capabilities, allowing you to perform vector searches directly on raw text data without using external embedding models to generate sparse vectors. For more information, refer to [​Full Text Search](https://zilliverse.feishu.cn/wiki/RQTRwhOVPiwnwokqr4scAtyfnBf).​

After vectorization, the data can be stored in Milvus for management and vector retrieval. The diagram below illustrates the basic process.​

![LlOrdRPh6oIF4dxo4RzchvDwn0g](请手动下载图片并替换)

:::info[📘 Notes​]

In addition to sparse vectors, Milvus also supports dense vectors and binary vectors. Dense vectors are ideal for capturing deep semantic relationships, while binary vectors excel in scenarios like quick similarity comparisons and content deduplication. For more information, refer to [​Dense Vector](https://zilliverse.feishu.cn/wiki/ARalwpaVDiCwDZkoSHtcPNgXnRg) and [​Binary Vector](https://zilliverse.feishu.cn/wiki/NTwawtvYdiXTkukbss7ccw2RnXc).​

:::

## Use sparse vectors in Milvus​{#use-sparse-vectors-in-milvus​}

Milvus supports representing sparse vectors in any of the following formats:​

- **Sparse Matrix (using the **`**scipy.sparse**`** class)**​

    ```Python
    from scipy.sparse import csr_matrix​
    ​
    # Create a sparse matrix​
    row = [0, 0, 1, 2, 2, 2]​
    col = [0, 2, 2, 0, 1, 2]​
    data = [1, 2, 3, 4, 5, 6]​
    sparse_matrix = csr_matrix((data, (row, col)), shape=(3, 3))​
    ​
    # Represent sparse vector using the sparse matrix​
    sparse_vector = sparse_matrix.getrow(0)​

    ```

- **List of Dictionaries (formatted as **`**{dimension_index: value, ...}**`**)**​

    <Tabs><TabItem value="Python" label="python" default>

    ```Python
    # Represent sparse vector using a dictionary​
    sparse_vector = [{1: 0.5, 100: 0.3, 500: 0.8, 1024: 0.2, 5000: 0.6}]​

    ```    

</TabItem>

    <TabItem value="Java" label="java">

    ```Java
    SortedMap<Long, Float> sparseVector = new TreeMap<>();​
    sparseVector.put(1L, 0.5f);​
    sparseVector.put(100L, 0.3f);​
    sparseVector.put(500L, 0.8f);​
    sparseVector.put(1024L, 0.2f);​
    sparseVector.put(5000L, 0.6f);​

    ```    

</TabItem></Tabs>

- **List of **Tuple** Iterators (formatted as **`**[(dimension_index, value)]**`**)**​

    ```Python
    # Represent sparse vector using a list of tuples​
    sparse_vector = [[(1, 0.5), (100, 0.3), (500, 0.8), (1024, 0.2), (5000, 0.6)]]​

    ```

### Add vector field​{#add-vector-field​}

To use sparse vectors in Milvus, define a field for storing sparse vectors when creating a collection. This process includes:​

1. Setting `datatype` to the supported sparse vector data type, `SPARSE_FLOAT_VECTOR`.​

2. No need to specify the dimension.​

<Tabs><TabItem value="Python" label="python" default>

```Python
from pymilvus import MilvusClient, DataType​
​
client = MilvusClient(uri="http://localhost:19530")​
​
client.drop_collection(collection_name="my_sparse_collection")​
​
schema = client.create_schema(​
    auto_id=True,​
    enable_dynamic_fields=True,​
)​
​
schema.add_field(field_name="pk", datatype=DataType.VARCHAR, is_primary=True, max_length=100)​
schema.add_field(field_name="sparse_vector", datatype=DataType.SPARSE_FLOAT_VECTOR)​

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
schema.addField(AddFieldReq.builder()​
        .fieldName("pk")​
        .dataType(DataType.VarChar)​
        .isPrimaryKey(true)​
        .autoID(true)​
        .maxLength(100)​
        .build());​
​
schema.addField(AddFieldReq.builder()​
        .fieldName("sparse_vector")​
        .dataType(DataType.SparseFloatVector)​
        .build());​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
import { DataType } from "@zilliz/milvus2-sdk-node";​
​
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
    name: "sparse_vector",​
    data_type: DataType.SparseFloatVector,​
  }​
];​
​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
export primaryField='{​
    "fieldName": "pk",​
    "dataType": "VarChar",​
    "isPrimary": true,​
    "elementTypeParams": {​
        "max_length": 100​
    }​
}'​
​
export vectorField='{​
    "fieldName": "sparse_vector",​
    "dataType": "SparseFloatVector"​
}'​
​
export schema="{​
    \"autoID\": true,​
    \"fields\": [​
        $primaryField,​
        $vectorField​
    ]​
}"​

```

</TabItem></Tabs>

In this example, a vector field named `sparse_vector` is added for storing sparse vectors. The data type of this field is `SPARSE_FLOAT_VECTOR`.​

### Set index params for vector field​{#set-index-params-for-vector-field​}

The process of creating an index for sparse vectors is similar to that for [dense vectors](https://zilliverse.feishu.cn/wiki/ARalwpaVDiCwDZkoSHtcPNgXnRg), but with differences in the specified index type (`index_type`), distance metric (`metric_type`), and index parameters (`params`).​

<Tabs><TabItem value="Python" label="python" default>

```Python
index_params = client.prepare_index_params()​
​
index_params.add_index(​
    field_name="sparse_vector",​
    index_name="sparse_inverted_index",​
    index_type="SPARSE_INVERTED_INDEX",​
    metric_type="IP",​
    params={"drop_ratio_build": 0.2},​
)​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
import io.milvus.v2.common.IndexParam;​
import java.util.*;​
​
List<IndexParam> indexes = new ArrayList<>();​
Map<String,Object> extraParams = new HashMap<>();​
extraParams.put("drop_ratio_build", 0.2);​
indexes.add(IndexParam.builder()​
        .fieldName("sparse_vector")​
        .indexName("sparse_inverted_index")​
        .indexType(IndexParam.IndexType.SPARSE_INVERTED_INDEX)​
        .metricType(IndexParam.MetricType.IP)​
        .extraParams(extraParams)​
        .build());​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
const indexParams = await client.createIndex({​
    index_name: 'sparse_inverted_index',​
    field_name: 'sparse_vector',​
    metric_type: MetricType.IP,​
    index_type: IndexType.SPARSE_WAND,​
    params: {​
      drop_ratio_build: 0.2,​
    },​
});​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
export indexParams='[​
        {​
            "fieldName": "sparse_vector",​
            "metricType": "IP",​
            "indexName": "sparse_inverted_index",​
            "indexType": "SPARSE_INVERTED_INDEX",​
            "params":{"drop_ratio_build": 0.2}​
        }​
    ]'​

```

</TabItem></Tabs>

In the example above:​

- An index of type `SPARSE_INVERTED_INDEX` is created for the sparse vector. For sparse vectors, you can specify `SPARSE_INVERTED_INDEX` or `SPARSE_WAND`. For details, refer to [​Sparse Vector Indexes](https://zilliverse.feishu.cn/wiki/GXbvwiLamir1vckA6u2c1KUFnMe).​

- For sparse vectors, `metric_type` only supports `IP` (Inner Product), used to measure the similarity between two sparse vectors. For more information on similarity, refer to [​Metric Types](https://zilliverse.feishu.cn/wiki/EOxmwUDxMiy2cpkOfIsc1dYzn4c).​

- `drop_ratio_build` is an optional index parameter specifically for sparse vectors. It controls the proportion of small vector values excluded during index building. For example, with `{"drop_ratio_build": 0.2}`, the smallest 20% of vector values will be excluded during index creation, reducing computational effort during searches.​

### Create collection​{#create-collection​}

Once the sparse vector and index settings are complete, you can create a collection that contains sparse vectors. The example below uses the <ins>`create_collection`</ins> method to create a collection named `my_sparse_collection`.​

<Tabs><TabItem value="Python" label="python" default>

```Python
client.create_collection(​
    collection_name="my_sparse_collection",​
    schema=schema,​
    index_params=index_params​
)​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
import io.milvus.v2.client.ConnectConfig;​
import io.milvus.v2.client.MilvusClientV2;​
​
MilvusClientV2 client = new MilvusClientV2(ConnectConfig.builder()​
        .uri("http://localhost:19530")​
        .build());​
        ​
CreateCollectionReq requestCreate = CreateCollectionReq.builder()​
        .collectionName("my_sparse_collection")​
        .collectionSchema(schema)​
        .indexParams(indexes)​
        .build();​
client.createCollection(requestCreate);​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
import { MilvusClient } from "@zilliz/milvus2-sdk-node";​
​
const client = new MilvusClient({​
    address: 'http://localhost:19530'​
});​
​
await client.createCollection({​
    collection_name: 'my_sparse_collection',​
    schema: schema,​
    index_params: indexParams​
});​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
curl --request POST \​
--url "${CLUSTER_ENDPOINT}/v2/vectordb/collections/create" \​
--header "Authorization: Bearer ${TOKEN}" \​
--header "Content-Type: application/json" \​
-d "{​
    \"collectionName\": \"my_sparse_collection\",​
    \"schema\": $schema,​
    \"indexParams\": $indexParams​
}"​

```

</TabItem></Tabs>

### Insert data​{#insert-data​}

After creating the collection, insert data containing sparse vectors.​

<Tabs><TabItem value="Python" label="python" default>

```Python
sparse_vectors = [​
    {"sparse_vector": {1: 0.5, 100: 0.3, 500: 0.8}},​
    {"sparse_vector": {10: 0.1, 200: 0.7, 1000: 0.9}},​
]​
​
client.insert(​
    collection_name="my_sparse_collection",​
    data=sparse_vectors​
)​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
import com.google.gson.Gson;​
import com.google.gson.JsonObject;​
import io.milvus.v2.service.vector.request.InsertReq;​
import io.milvus.v2.service.vector.response.InsertResp;​
​
List<JsonObject> rows = new ArrayList<>();​
Gson gson = new Gson();​
{​
    JsonObject row = new JsonObject();​
    SortedMap<Long, Float> sparse = new TreeMap<>();​
    sparse.put(1L, 0.5f);​
    sparse.put(100L, 0.3f);​
    sparse.put(500L, 0.8f);​
    row.add("sparse_vector", gson.toJsonTree(sparse));​
    rows.add(row);​
}​
{​
    JsonObject row = new JsonObject();​
    SortedMap<Long, Float> sparse = new TreeMap<>();​
    sparse.put(10L, 0.1f);​
    sparse.put(200L, 0.7f);​
    sparse.put(1000L, 0.9f);​
    row.add("sparse_vector", gson.toJsonTree(sparse));​
    rows.add(row);​
}​
​
InsertResp insertR = client.insert(InsertReq.builder()​
        .collectionName("my_sparse_collection")​
        .data(rows)​
        .build());​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
const data = [​
  { sparse_vector: { "1": 0.5, "100": 0.3, "500": 0.8 } },​
  { sparse_vector: { "10": 0.1, "200": 0.7, "1000": 0.9 } },​
];​
client.insert({​
  collection_name: "my_sparse_collection",​
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
        {"sparse_vector": {"1": 0.5, "100": 0.3, "500": 0.8}},​
        {"sparse_vector": {"10": 0.1, "200": 0.7, "1000": 0.9}}        ​
    ],​
    "collectionName": "my_sparse_collection"​
}'​
​
## {"code":0,"cost":0,"data":{"insertCount":2,"insertIds":["453577185629572534","453577185629572535"]}}​

```

</TabItem></Tabs>

### Perform similarity search​{#perform-similarity-search​}

To perform similarity search using sparse vectors, prepare the query vector and search parameters.​

```Python
# Prepare search parameters​
search_params = {​
    "params": {"drop_ratio_search": 0.2},  # Additional optional search parameters​
}​
​
# Prepare the query vector​
query_vector = [{1: 0.2, 50: 0.4, 1000: 0.7}]​

```

In this example, `drop_ratio_search` is an optional parameter specifically for sparse vectors, allowing fine-tuning of small values in the query vector during the search. For example, with `{"drop_ratio_search": 0.2}`, the smallest 20% of values in the query vector will be ignored during the search.​

Then, execute the similarity search using the `search` method:​

<Tabs><TabItem value="Python" label="python" default>

```Python
res = client.search(​
    collection_name="my_sparse_collection",​
    data=query_vector,​
    limit=3,​
    output_fields=["pk"],​
    search_params=search_params,​
)​
​
print(res)​
​
# Output​
# data: ["[{'id': '453718927992172266', 'distance': 0.6299999952316284, 'entity': {'pk': '453718927992172266'}}, {'id': '453718927992172265', 'distance': 0.10000000149011612, 'entity': {'pk': '453718927992172265'}}]"]​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
import io.milvus.v2.service.vector.request.SearchReq;​
import io.milvus.v2.service.vector.request.data.SparseFloatVec;​
import io.milvus.v2.service.vector.response.SearchResp;​
​
Map<String,Object> searchParams = new HashMap<>();​
searchParams.put("drop_ratio_search", 0.2);​
​
SortedMap<Long, Float> sparse = new TreeMap<>();​
sparse.put(10L, 0.1f);​
sparse.put(200L, 0.7f);​
sparse.put(1000L, 0.9f);​
​
SparseFloatVec queryVector = new SparseFloatVec(sparse);​
​
SearchResp searchR = client.search(SearchReq.builder()​
        .collectionName("my_sparse_collection")​
        .data(Collections.singletonList(queryVector))​
        .annsField("sparse_vector")​
        .searchParams(searchParams)​
        .topK(3)​
        .outputFields(Collections.singletonList("pk"))​
        .build());​
        ​
System.out.println(searchR.getSearchResults());​
​
// Output​
//​
// [[SearchResp.SearchResult(entity={pk=453444327741536759}, score=1.31, id=453444327741536759), SearchResp.SearchResult(entity={pk=453444327741536756}, score=1.31, id=453444327741536756), SearchResp.SearchResult(entity={pk=453444327741536753}, score=1.31, id=453444327741536753)]]​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
client.search({​
    collection_name: 'my_sparse_collection',​
    data: {1: 0.2, 50: 0.4, 1000: 0.7},​
    limit: 3,​
    output_fields: ['pk'],​
    params: {​
        drop_ratio_search: 0.2​
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
    "collectionName": "my_sparse_collection",​
    "data": [​
        {"1": 0.2, "50": 0.4, "1000": 0.7}​
    ],​
    "annsField": "sparse_vector",​
    "limit": 3,​
    "searchParams":{​
        "params":{"drop_ratio_search": 0.2}​
    },​
    "outputFields": ["pk"]​
}'​
​
## {"code":0,"cost":0,"data":[{"distance":0.63,"id":"453577185629572535","pk":"453577185629572535"},{"distance":0.1,"id":"453577185629572534","pk":"453577185629572534"}]}​

```

</TabItem></Tabs>

For more information on similarity search parameters, refer to [​Basic ANN Search](https://zilliverse.feishu.cn/wiki/BaGlwzDmyiyVvVk6NurcFclInCd).​


# Binary Vector​

Binary vectors are a special form of data representation that convert traditional high-dimensional floating-point vectors into binary vectors containing only 0s and 1s. This transformation not only compresses the size of the vector but also reduces storage and computational costs while retaining semantic information. When precision for non-critical features is not essential, binary vectors can effectively maintain most of the integrity and utility of the original floating-point vectors.​

Binary vectors have a wide range of applications, particularly in situations where computational efficiency and storage optimization are crucial. In large-scale AI systems, such as search engines or recommendation systems, real-time processing of massive amounts of data is key. By reducing the size of the vectors, binary vectors help lower latency and computational costs without significantly sacrificing accuracy. Additionally, binary vectors are useful in resource-constrained environments, such as mobile devices and embedded systems, where memory and processing power are limited. Through the use of binary vectors, complex AI functions can be implemented in these restricted settings while maintaining high performance.​

## Overview​{#overview​}

Binary vectors are a method of encoding complex objects (like images, text, or audio) into fixed-length binary values. In Milvus, binary vectors are typically represented as bit arrays or byte arrays. For example, an 8-dimensional binary vector can be represented as `[1, 0, 1, 1, 0, 0, 1, 0]`.​

The diagram below shows how binary vectors represent the presence of keywords in text content. In this example, a 10-dimensional binary vector is used to represent two different texts (**Text 1** and **Text 2**), where each dimension corresponds to a word in the vocabulary: 1 indicates the presence of the word in the text, while 0 indicates its absence.​

![PQlXdkDBpoRn27x700CcFOEQnrg](请手动下载图片并替换)

Binary vectors have the following characteristics:​

- **Efficient Storage:** Each dimension requires only 1 bit of storage, significantly reducing storage space.​

- **Fast Computation:** Similarity between vectors can be quickly calculated using bitwise operations like XOR.​

- **Fixed Length:** The length of the vector remains constant regardless of the original text length, making indexing and retrieval easier.​

- **Simple and Intuitive:** Directly reflects the presence of keywords, making it suitable for certain specialized retrieval tasks.​

Binary vectors can be generated through various methods. In text processing, predefined vocabularies can be used to set corresponding bits based on word presence. For image processing, perceptual hashing algorithms (like [pHash](https://en.wikipedia.org/wiki/Perceptual_hashing)) can generate binary features of images. In machine learning applications, model outputs can be binarized to obtain binary vector representations.​

After binary vectorization, the data can be stored in Milvus for management and vector retrieval. The diagram below shows the basic process.​

![LrvVdqoI0opvJ8xgmlFcoNENnsc](请手动下载图片并替换)

:::info[📘 Notes​]

Although binary vectors excel in specific scenarios, they have limitations in their expressive capability, making it difficult to capture complex semantic relationships. Therefore, in real-world scenarios, binary vectors are often used alongside other vector types to balance efficiency and expressiveness. For more information, refer to [​Dense Vector](https://zilliverse.feishu.cn/wiki/ARalwpaVDiCwDZkoSHtcPNgXnRg) and [​Sparse Vector](https://zilliverse.feishu.cn/wiki/JbPDwHqd0iZZSuk5tYicGqKbn9c).​

:::

## Use binary vectors in Milvus​{#use-binary-vectors-in-milvus​}

### Add vector field​{#add-vector-field​}

To use binary vectors in Milvus, first define a vector field for storing binary vectors when creating a collection. This process includes:​

1. Setting `datatype` to the supported binary vector data type, i.e., `BINARY_VECTOR`.​

2. Specifying the vector's dimensions using the `dim` parameter. Note that `dim` must be a multiple of 8 as binary vectors must be converted into a byte array when inserting. Every 8 boolean values (0 or 1) will be packed into 1 byte. For example, if `dim=128`, a 16-byte array is required for insertion.​

<Tabs><TabItem value="Python" label="python" default>

```Python
from pymilvus import MilvusClient, DataType​
​
client = MilvusClient(uri="http://localhost:19530")​
​
schema = client.create_schema(​
    auto_id=True,​
    enable_dynamic_fields=True,​
)​
​
schema.add_field(field_name="pk", datatype=DataType.VARCHAR, is_primary=True, max_length=100)​
schema.add_field(field_name="binary_vector", datatype=DataType.BINARY_VECTOR, dim=128)​

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
        .fieldName("binary_vector")​
        .dataType(DataType.BinaryVector)​
        .dimension(128)​
        .build());​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
import { DataType } from "@zilliz/milvus2-sdk-node";​
​
schema.push({​
  name: "binary vector",​
  data_type: DataType.BinaryVector,​
  dim: 128,​
});​

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
    "fieldName": "binary_vector",​
    "dataType": "BinaryVector",​
    "elementTypeParams": {​
        "dim": 128​
    }​
}'​
​
export schema="{​
    \"autoID\": true,​
    \"fields\": [​
        $primaryField,​
        $vectorField​
    ],​
    \"enableDynamicField\": true​
}"​
​

```

</TabItem></Tabs>

In this example, a vector field named `binary_vector` is added for storing binary vectors. The data type of this field is `BINARY_VECTOR`, with a dimension of 128.​

### Set index params for vector field​{#set-index-params-for-vector-field​}

To speed up searches, an index must be created for the binary vector field. Indexing can significantly enhance the retrieval efficiency of large-scale vector data.​

<Tabs><TabItem value="Python" label="python" default>

```Python
index_params = client.prepare_index_params()​
​
index_params.add_index(​
    field_name="binary_vector",​
    index_name="binary_vector_index",​
    index_type="BIN_IVF_FLAT",​
    metric_type="HAMMING",​
    params={"nlist": 128}​
)​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
import io.milvus.v2.common.IndexParam;​
import java.util.*;​
​
List<IndexParam> indexParams = new ArrayList<>();​
Map<String,Object> extraParams = new HashMap<>();​
extraParams.put("nlist",128);​
indexParams.add(IndexParam.builder()​
        .fieldName("binary_vector")​
        .indexType(IndexParam.IndexType.BIN_IVF_FLAT)​
        .metricType(IndexParam.MetricType.HAMMING)​
        .extraParams(extraParams)​
        .build());​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
import { MetricType, IndexType } from "@zilliz/milvus2-sdk-node";​
​
const indexParams = {​
  indexName: "binary_vector_index",​
  field_name: "binary_vector",​
  metric_type: MetricType.HAMMING,​
  index_type: IndexType.BIN_IVF_FLAT,​
  params: {​
    nlist: 128,​
  },​
};​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
export indexParams='[​
        {​
            "fieldName": "binary_vector",​
            "metricType": "HAMMING",​
            "indexName": "binary_vector_index",​
            "indexType": "BIN_IVF_FLAT",​
            "params":{"nlist": 128}​
        }​
    ]'​

```

</TabItem></Tabs>

In the example above, an index named `binary_vector_index` is created for the `binary_vector` field, using the `BIN_IVF_FLAT` index type. The `metric_type` is set to `HAMMING`, indicating that Hamming distance is used for similarity measurement.​

Besides `BIN_IVF_FLAT`, Milvus supports other index types for binary vectors. For more details, refer to [​Binary Vector Indexes](https://zilliverse.feishu.cn/wiki/FqFNw9gKdiLpEkkoW16cYMWgnIc). Additionally, Milvus supports other similarity metrics for binary vectors. For more information, refer to [​Metric Types](https://zilliverse.feishu.cn/wiki/EOxmwUDxMiy2cpkOfIsc1dYzn4c).​

### Create collection​{#create-collection​}

Once the binary vector and index settings are complete, create a collection that contains binary vectors. The example below uses the `create_collection` method to create a collection named `my_binary_collection`.​

<Tabs><TabItem value="Python" label="python" default>

```Python
client.create_collection(​
    collection_name="my_binary_collection",​
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
        .collectionName("my_binary_collection")​
        .collectionSchema(schema)​
        .indexParams(indexParams)​
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
    collection_name: 'my_dense_collection',​
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
    \"collectionName\": \"my_binary_collection\",​
    \"schema\": $schema,​
    \"indexParams\": $indexParams​
}"​

```

</TabItem></Tabs>

### Insert data​{#insert-data​}

After creating the collection, use the `insert` method to add data containing binary vectors. Note that binary vectors should be provided in the form of a byte array, where each byte represents 8 boolean values.​

For example, for a 128-dimensional binary vector, a 16-byte array is required (since 128 bits ÷ 8 bits/byte = 16 bytes). Below is an example code for inserting data:​

<Tabs><TabItem value="Python" label="python" default>

```Python
def convert_bool_list_to_bytes(bool_list):​
    if len(bool_list) % 8 != 0:​
        raise ValueError("The length of a boolean list must be a multiple of 8")​
​
    byte_array = bytearray(len(bool_list) // 8)​
    for i, bit in enumerate(bool_list):​
        if bit == 1:​
            index = i // 8​
            shift = i % 8​
            byte_array[index] |= (1 << shift)​
    return bytes(byte_array)​
​
​
bool_vectors = [​
    [1, 0, 0, 1, 1, 0, 1, 1, 0, 1, 0, 1, 0, 1, 0, 0] + [0] * 112,​
    [0, 1, 0, 1, 0, 1, 0, 0, 1, 1, 0, 0, 1, 1, 0, 1] + [0] * 112,​
]​
​
data = [{"binary_vector": convert_bool_list_to_bytes(bool_vector) for bool_vector in bool_vectors}]​
​
client.insert(​
    collection_name="my_binary_collection",​
    data=data​
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
private static byte[] convertBoolArrayToBytes(boolean[] booleanArray) {​
    byte[] byteArray = new byte[booleanArray.length / Byte.SIZE];​
    for (int i = 0; i < booleanArray.length; i++) {​
        if (booleanArray[i]) {​
            int index = i / Byte.SIZE;​
            int shift = i % Byte.SIZE;​
            byteArray[index] |= (byte) (1 << shift);​
        }​
    }​
​
    return byteArray;​
}​
​
List<JsonObject> rows = new ArrayList<>();​
Gson gson = new Gson();​
{​
    boolean[] boolArray = {true, false, false, true, true, false, true, true, false, true, false, false, true, true, false, true};​
    JsonObject row = new JsonObject();​
    row.add("binary_vector", gson.toJsonTree(convertBoolArrayToBytes(boolArray)));​
    rows.add(row);​
}​
{​
    boolean[] boolArray = {false, true, false, true, false, true, false, false, true, true, false, false, true, true, false, true};​
    JsonObject row = new JsonObject();​
    row.add("binary_vector", gson.toJsonTree(convertBoolArrayToBytes(boolArray)));​
    rows.add(row);​
}​
​
InsertResp insertR = client.insert(InsertReq.builder()​
        .collectionName("my_binary_collection")​
        .data(rows)​
        .build());​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
const data = [​
  { binary_vector: [1, 0, 0, 1, 1, 0, 1, 1, 0, 1, 0, 0, 1, 1, 0, 1] },​
  { binary_vector: [1, 0, 0, 1, 1, 0, 1, 1, 0, 1, 0, 0, 1, 1, 0, 1] },​
];​
​
client.insert({​
  collection_name: "my_binary_collection",​
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
-d "{​
    \"data\": $data,​
    \"collectionName\": \"my_binary_collection\"​
}"​

```

</TabItem></Tabs>

### Perform similarity search​{#perform-similarity-search​}

Similarity search is one of the core features of Milvus, allowing you to quickly find data that is most similar to a query vector based on the distance between vectors. To perform a similarity search using binary vectors, prepare the query vector and search parameters, then call the `search` method.​

During search operations, binary vectors must also be provided in the form of a byte array. Ensure that the dimensionality of the query vector matches the dimension specified when defining `dim` and that every 8 boolean values are converted into 1 byte.​

<Tabs><TabItem value="Python" label="python" default>

```Python
search_params = {​
    "params": {"nprobe": 10}​
}​
​
query_bool_list = [1, 0, 0, 1, 1, 0, 1, 1, 0, 1, 0, 1, 0, 1, 0, 0] + [0] * 112​
query_vector = convert_bool_list_to_bytes(query_bool_list)​
​
res = client.search(​
    collection_name="my_binary_collection",​
    data=[query_vector],​
    anns_field="binary_vector",​
    search_params=search_params,​
    limit=5,​
    output_fields=["pk"]​
)​
​
print(res)​
​
# Output​
# data: ["[{'id': '453718927992172268', 'distance': 10.0, 'entity': {'pk': '453718927992172268'}}]"] ​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
import io.milvus.v2.service.vector.request.SearchReq;​
import io.milvus.v2.service.vector.request.data.BinaryVec;​
import io.milvus.v2.service.vector.response.SearchResp;​
​
Map<String,Object> searchParams = new HashMap<>();​
searchParams.put("nprobe",10);​
​
boolean[] boolArray = {true, false, false, true, true, false, true, true, false, true, false, false, true, true, false, true};​
BinaryVec queryVector = new BinaryVec(convertBoolArrayToBytes(boolArray));​
​
SearchResp searchR = client.search(SearchReq.builder()​
        .collectionName("my_binary_collection")​
        .data(Collections.singletonList(queryVector))​
        .annsField("binary_vector")​
        .searchParams(searchParams)​
        .topK(5)​
        .outputFields(Collections.singletonList("pk"))​
        .build());​
        ​
 System.out.println(searchR.getSearchResults());​
 ​
 // Output​
 //​
 // [[SearchResp.SearchResult(entity={pk=453444327741536775}, score=0.0, id=453444327741536775), SearchResp.SearchResult(entity={pk=453444327741536776}, score=7.0, id=453444327741536776)]]​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
query_vector = [1,0,1,0,1,1,1,1,1,1,1,1];​
​
client.search({​
    collection_name: 'my_binary_collection',​
    data: query_vector,​
    limit: 5,​
    output_fields: ['pk'],​
    params: {​
        nprobe: 10​
    }​
});​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
export searchParams='{​
        "params":{"nprobe":10}​
    }'​
​
curl --request POST \​
--url "${CLUSTER_ENDPOINT}/v2/vectordb/entities/search" \​
--header "Authorization: Bearer ${TOKEN}" \​
--header "Content-Type: application/json" \​
-d "{​
    \"collectionName\": \"my_binary_collection\",​
    \"data\": $data,​
    \"annsField\": \"binary_vector\",​
    \"limit\": 5,​
    \"searchParams\":$searchParams,​
    \"outputFields\": [\"pk\"]​
}"​

```

</TabItem></Tabs>

For more information on similarity search parameters, refer to [​Basic ANN Search](https://zilliverse.feishu.cn/wiki/BaGlwzDmyiyVvVk6NurcFclInCd).​

​


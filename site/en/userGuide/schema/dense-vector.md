# Dense Vector​

Dense vectors are numerical data representations widely used in machine learning and data analysis. They consist of arrays with real numbers, where most or all elements are non-zero. Compared to sparse vectors, dense vectors contain more information at the same dimensional level, as each dimension holds meaningful values. This representation can effectively capture complex patterns and relationships, making data easier to analyze and process in high-dimensional spaces. Dense vectors typically have a fixed number of dimensions, ranging from a few dozen to several hundred or even thousands, depending on the specific application and requirements.​

Dense vectors are mainly used in scenarios that require understanding the semantics of data, such as semantic search and recommendation systems. In semantic search, dense vectors help capture the underlying connections between queries and documents, improving the relevance of search results. In recommendation systems, they aid in identifying similarities between users and items, offering more personalized suggestions.​

## Overview​{#overview​}

Dense vectors are typically represented as arrays of floating-point numbers with a fixed length, such as `[0.2, 0.7, 0.1, 0.8, 0.3, ..., 0.5]`. The dimensionality of these vectors usually ranges from hundreds to thousands, such as 128, 256, 768, or 1024. Each dimension captures specific semantic features of an object, making it applicable to various scenarios through similarity calculations.​

![Kh8Fdoy7PoWitsxH1j7cguKRnmh](请手动下载图片并替换)

The image above illustrates the representation of dense vectors in a 2D space. Although dense vectors in real-world applications often have much higher dimensions, this 2D illustration effectively conveys several key concepts:​

- **Multidimensional Representation:** Each point represents a conceptual object (like **Milvus**, **vector database**, **retrieval system**, etc.), with its position determined by the values of its dimensions.​

- **Semantic Relationships:** The distances between points reflect the semantic similarity between concepts. Closer points indicate concepts that are more semantically related.​

- **Clustering Effect:** Related concepts (such as **Milvus**, **vector database**, and **retrieval system**) are positioned close to each other in space, forming a semantic cluster.​

Below is an example of a real dense vector representing the text `"Milvus is an efficient vector database"`:​

```JSON
[​
    -0.013052909,​
    0.020387933,​
    -0.007869,​
    -0.11111383,​
    -0.030188112,​
    -0.0053388323,​
    0.0010654867,​
    0.072027855,​
    // ... more dimensions​
]​
​

```

Dense vectors can be generated using various [embedding](https://en.wikipedia.org/wiki/Embedding) models, such as CNN models (like [ResNet](https://pytorch.org/hub/pytorch_vision_resnet/), [VGG](https://pytorch.org/vision/stable/models/vgg.html)) for images and language models (like [BERT](https://en.wikipedia.org/wiki/BERT_(language_model)), [Word2Vec](https://en.wikipedia.org/wiki/Word2vec)) for text. These models transform raw data into points in high-dimensional space, capturing the semantic features of the data. Additionally, Milvus offers convenient methods to help users generate and process dense vectors, as detailed in Embeddings.​

Once data is vectorized, it can be stored in Milvus for management and vector retrieval. The diagram below shows the basic process.​

![R93pdmHBAoGSBCxTDEYcmNNsnlb](请手动下载图片并替换)

:::info[📘 Notes​]

Besides dense vectors, Milvus also supports sparse vectors and binary vectors. Sparse vectors are suitable for precise matches based on specific terms, such as keyword search and term matching, while binary vectors are commonly used for efficiently handling binarized data, such as image pattern matching and certain hashing applications. For more information, refer to [​Binary Vector](https://zilliverse.feishu.cn/wiki/NTwawtvYdiXTkukbss7ccw2RnXc) and [​Sparse Vector](https://zilliverse.feishu.cn/wiki/JbPDwHqd0iZZSuk5tYicGqKbn9c).​

:::

## Use dense vectors in Milvus​{#use-dense-vectors-in-milvus​}

### Add vector field​{#add-vector-field​}

To use dense vectors in Milvus, first define a vector field for storing dense vectors when creating a collection. This process includes:​

1. Setting `datatype` to a supported dense vector data type. For supported dense vector data types, see Data Types.​

2. Specifying the dimensions of the dense vector using the `dim` parameter.​

In the example below, we add a vector field named `dense_vector` to store dense vectors. The field's data type is `FLOAT_VECTOR`, with a dimension of `4`.​

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
schema.add_field(field_name="dense_vector", datatype=DataType.FLOAT_VECTOR, dim=4)​

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
        .fieldName("dense_vector")​
        .dataType(DataType.FloatVector)​
        .dimension(4)​
        .build());​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
import { DataType } from "@zilliz/milvus2-sdk-node";​
​
schema.push({​
  name: "dense_vector",​
  data_type: DataType.FloatVector,​
  dim: 128,​
});​
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
    "fieldName": "dense_vector",​
    "dataType": "FloatVector",​
    "elementTypeParams": {​
        "dim": 4​
    }​
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

**Supported data types for dense vector fields**:​

<table data-block-token="JAUNdM7ZcoSgwDxy615cBt7Snme"><thead><tr><th data-block-token="Lw4Ld3RlSowJgAxAnbNc8oR5nPh" colspan="1" rowspan="1"><p data-block-token="DdlrdLU6Yo550Mx5TObcraycnyb">Data Type​</p>

</th><th data-block-token="TGq6d0VpNoRoW2xg8Kac7nLZnab" colspan="1" rowspan="1"><p data-block-token="ON8LdxqpPoPktOxcEZ2cvC9Unkg">Description​</p>

</th></tr></thead><tbody><tr><td data-block-token="RiuCd0ekQoV004xdoOMcxYXfnvg" colspan="1" rowspan="1"><p data-block-token="D0ivdW1gVoSFLRxo1q9cmNK2nrb"><code>`FLOAT_VECTOR`</code>​</p>

</td><td data-block-token="E3pTdyOLUoxAehx1Tt1cTfaUn6f" colspan="1" rowspan="1"><p data-block-token="O6OAdU97DoBnEsxkIMzcZe71nPc">Stores 32-bit floating-point numbers, commonly used for representing real numbers in scientific computations and machine learning. Ideal for scenarios requiring high precision, such as distinguishing similar vectors.​</p>

</td></tr><tr><td data-block-token="JmvQdldr7oBr0cxhObWcvoH9nRe" colspan="1" rowspan="1"><p data-block-token="RqhOdkiICop2sGxmEplciH9HnAb"><code>`FLOAT16_VECTOR`</code>​</p>

</td><td data-block-token="P7Q1dhgCSo9ONKxS7Uucy3Wcn0f" colspan="1" rowspan="1"><p data-block-token="AKGld9vXtobxivx4LNgcszcmnMc">Stores 16-bit half-precision floating-point numbers, used for deep learning and GPU computations. It saves storage space in scenarios where precision is less critical, such as in the low-precision recall phase of recommendation systems.​</p>

</td></tr><tr><td data-block-token="SuMwdk8fLohKcVxGPrpcHe08npd" colspan="1" rowspan="1"><p data-block-token="RMrjd1l4yoxYpkxI599cPc5Qn7X"><code>`BFLOAT16_VECTOR`</code>​</p>

</td><td data-block-token="VMfrdtFHToAHFaxS8gfcdSwunSb" colspan="1" rowspan="1"><p data-block-token="FrLVdqlrtoau2axEYevc5kDBnVr">Stores 16-bit Brain Floating Point (bfloat16) numbers, offering the same range of exponents as Float32 but with reduced precision. Suitable for scenarios that need to process large volumes of vectors quickly, such as large-scale image retrieval.​</p>

</td></tr></tbody></table>

### Set index params for vector field​{#set-index-params-for-vector-field​}

To accelerate semantic searches, an index must be created for the vector field. Indexing can significantly improve the retrieval efficiency of large-scale vector data.​

<Tabs><TabItem value="Python" label="python" default>

```Python
index_params = client.prepare_index_params()​
​
index_params.add_index(​
    field_name="dense_vector",​
    index_name="dense_vector_index",​
    index_type="IVF_FLAT",​
    metric_type="IP",​
    params={"nlist": 128}​
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
extraParams.put("nlist",128);​
indexes.add(IndexParam.builder()​
        .fieldName("dense_vector")​
        .indexType(IndexParam.IndexType.IVF_FLAT)​
        .metricType(IndexParam.MetricType.IP)​
        .extraParams(extraParams)​
        .build());​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
import { MetricType, IndexType } from "@zilliz/milvus2-sdk-node";​
​
const indexParams = {​
    index_name: 'dense_vector_index',​
    field_name: 'dense_vector',​
    metric_type: MetricType.IP,​
    index_type: IndexType.IVF_FLAT,​
    params: {​
      nlist: 128​
    },​
};​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
export indexParams='[​
        {​
            "fieldName": "dense_vector",​
            "metricType": "IP",​
            "indexName": "dense_vector_index",​
            "indexType": "IVF_FLAT",​
            "params":{"nlist": 128}​
        }​
    ]'​

```

</TabItem></Tabs>

In the example above, an index named `dense_vector_index` is created for the `dense_vector` field using the `IVF_FLAT` index type. The `metric_type` is set to `IP`, indicating that inner product will be used as the distance metric.​

Milvus supports other index types as well. For more details, refer to [​Floating Vector Indexes](https://zilliverse.feishu.cn/wiki/ODCkw5QIlizNizkZOxfcqvvNngc). Additionally, Milvus supports other metric types. For more information, refer to [​Metric Types](https://zilliverse.feishu.cn/wiki/EOxmwUDxMiy2cpkOfIsc1dYzn4c).​

### Create collection​{#create-collection​}

Once the dense vector and index param settings are complete, you can create a collection containing dense vectors. The example below uses the `create_collection` method to create a collection named `my_dense_collection`.​

<Tabs><TabItem value="Python" label="python" default>

```Python
client.create_collection(​
    collection_name="my_dense_collection",​
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
        .collectionName("my_dense_collection")​
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
    collection_name: 'my_dense_collection',​
    schema: schema,​
    index_params: indexParams​
});​
​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
curl --request POST \​
--url "${CLUSTER_ENDPOINT}/v2/vectordb/collections/create" \​
--header "Authorization: Bearer ${TOKEN}" \​
--header "Content-Type: application/json" \​
-d "{​
    \"collectionName\": \"my_dense_collection\",​
    \"schema\": $schema,​
    \"indexParams\": $indexParams​
}"​

```

</TabItem></Tabs>

### Insert data​{#insert-data​}

After creating the collection, use the `insert` method to add data containing dense vectors. Ensure that the dimensionality of the dense vectors being inserted matches the `dim` value defined when adding the dense vector field.​

<Tabs><TabItem value="Python" label="python" default>

```Python
data = [​
    {"dense_vector": [0.1, 0.2, 0.3, 0.7]},​
    {"dense_vector": [0.2, 0.3, 0.4, 0.8]},​
]​
​
client.insert(​
    collection_name="my_dense_collection",​
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
List<JsonObject> rows = new ArrayList<>();​
Gson gson = new Gson();​
rows.add(gson.fromJson("{\"dense_vector\": [0.1, 0.2, 0.3, 0.4]}", JsonObject.class));​
rows.add(gson.fromJson("{\"dense_vector\": [0.2, 0.3, 0.4, 0.5]}", JsonObject.class));​
​
InsertResp insertR = client.insert(InsertReq.builder()​
        .collectionName("my_dense_collection")​
        .data(rows)​
        .build());​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
const data = [​
  { dense_vector: [0.1, 0.2, 0.3, 0.7] },​
  { dense_vector: [0.2, 0.3, 0.4, 0.8] },​
];​
​
client.insert({​
  collection_name: "my_dense_collection",​
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
        {"dense_vector": [0.1, 0.2, 0.3, 0.4]},​
        {"dense_vector": [0.2, 0.3, 0.4, 0.5]}        ​
    ],​
    "collectionName": "my_dense_collection"​
}'​
​
## {"code":0,"cost":0,"data":{"insertCount":2,"insertIds":["453577185629572531","453577185629572532"]}}​

```

</TabItem></Tabs>

### Perform similarity search​{#perform-similarity-search​}

Semantic search based on dense vectors is one of the core features of Milvus, allowing you to quickly find data that is most similar to a query vector based on the distance between vectors. To perform a similarity search, prepare the query vector and search parameters, then call the `search` method.​

<Tabs><TabItem value="Python" label="python" default>

```Python
search_params = {​
    "params": {"nprobe": 10}​
}​
​
query_vector = [0.1, 0.2, 0.3, 0.7]​
​
res = client.search(​
    collection_name="my_dense_collection",​
    data=[query_vector],​
    anns_field="dense_vector",​
    search_params=search_params,​
    limit=5,​
    output_fields=["pk"]​
)​
​
print(res)​
​
# Output​
# data: ["[{'id': '453718927992172271', 'distance': 0.7599999904632568, 'entity': {'pk': '453718927992172271'}}, {'id': '453718927992172270', 'distance': 0.6299999952316284, 'entity': {'pk': '453718927992172270'}}]"]​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
import io.milvus.v2.service.vector.request.data.FloatVec;​
​
Map<String,Object> searchParams = new HashMap<>();​
searchParams.put("nprobe",10);​
​
FloatVec queryVector = new FloatVec(new float[]{0.1f, 0.3f, 0.3f, 0.4f});​
​
SearchResp searchR = client.search(SearchReq.builder()​
        .collectionName("my_dense_collection")​
        .data(Collections.singletonList(queryVector))​
        .annsField("dense_vector")​
        .searchParams(searchParams)​
        .topK(5)​
        .outputFields(Collections.singletonList("pk"))​
        .build());​
        ​
System.out.println(searchR.getSearchResults());​
​
// Output​
//​
// [[SearchResp.SearchResult(entity={pk=453444327741536779}, score=0.65, id=453444327741536779), SearchResp.SearchResult(entity={pk=453444327741536778}, score=0.65, id=453444327741536778)]]​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
query_vector = [0.1, 0.2, 0.3, 0.7];​
​
client.search({​
    collection_name: my_dense_collection,​
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
curl --request POST \​
--url "${CLUSTER_ENDPOINT}/v2/vectordb/entities/search" \​
--header "Authorization: Bearer ${TOKEN}" \​
--header "Content-Type: application/json" \​
-d '{​
    "collectionName": "my_dense_collection",​
    "data": [​
        [0.1, 0.2, 0.3, 0.7]​
    ],​
    "annsField": "dense_vector",​
    "limit": 5,​
    "searchParams":{​
        "params":{"nprobe":10}​
    },​
    "outputFields": ["pk"]​
}'​
​
## {"code":0,"cost":0,"data":[{"distance":0.55,"id":"453577185629572532","pk":"453577185629572532"},{"distance":0.42,"id":"453577185629572531","pk":"453577185629572531"}]}​

```

</TabItem></Tabs>

For more information on similarity search parameters, refer to [​Basic ANN Search](https://zilliverse.feishu.cn/wiki/BaGlwzDmyiyVvVk6NurcFclInCd).​


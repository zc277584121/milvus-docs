# Create Collection​

You can create a collection by defining its schema, index parameters, metric type, and whether to load it upon creation. This page introduces how to create a collection from scratch.​

## Overview​{#overview​}

A collection is a two-dimensional table with fixed columns and variant rows. Each column represents a field, and each row represents an entity. A schema is required to implement such structural data management. Every entity to insert has to meet the constraints defined in the schema.​

You can determine every aspect of a collection, including its schema, index parameters, metric type, and whether to load it upon creation to ensure that the collection fully meets your requirements.​

To create a collection, you need to​

- [Create schema](https://zilliverse.feishu.cn/wiki/EmcowmwYpiFbWgkmnqfcMf3knVc#Gf3wdrWLKoAZkIxtN38cagsmn3g)​

- [Set index parameters](https://zilliverse.feishu.cn/wiki/EmcowmwYpiFbWgkmnqfcMf3knVc#QVtAdf12roqTWxxEa5jcv6ctnag) (Optional)​

- [Create collection](https://zilliverse.feishu.cn/wiki/EmcowmwYpiFbWgkmnqfcMf3knVc#PLEDdr4MdouyYixXru2cv4fPnlb)​

## Create Schema​{#create-schema​}

A schema defines the data structure of a collection. When creating a collection, you need to design the schema based on your requirements. For details, refer to [​Schema Explained](https://zilliverse.feishu.cn/wiki/Vs4YwNnvzitoQ8kunlGcWMJInbf).​

The following code snippets create a schema with the enabled dynamic field and three mandatory fields named `my_id`, `my_vector`, and `my_varchar`.​

:::info[📘 Notes​]

You can set default values for any scalar field and make it nullable. For details, refer to  [​Nullable & Default](https://zilliverse.feishu.cn/wiki/DjROwgK6ziCf7Rkoji6ccyEUnsg).​

:::

<Tabs><TabItem value="Python" label="python" default>

```Python
# 3. Create a collection in customized setup mode​
from pymilvus import MilvusClient, DataType​
​
client = MilvusClient(​
    uri="http://localhost:19530",​
    token="root:Milvus"​
)​
​
# 3.1. Create schema​
schema = MilvusClient.create_schema(​
    auto_id=False,​
    enable_dynamic_field=True,​
)​
​
# 3.2. Add fields to schema​
schema.add_field(field_name="my_id", datatype=DataType.INT64, is_primary=True)​
schema.add_field(field_name="my_vector", datatype=DataType.FLOAT_VECTOR, dim=5)​
schema.add_field(field_name="my_varchar", datatype=DataType.VARCHAR, max_length=512)​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
import io.milvus.v2.common.DataType;​
import io.milvus.v2.client.ConnectConfig;​
import io.milvus.v2.client.MilvusClientV2;​
import io.milvus.v2.service.collection.request.AddFieldReq;​
import io.milvus.v2.service.collection.request.CreateCollectionReq;​
​
String CLUSTER_ENDPOINT = "http://localhost:19530";​
String TOKEN = "root:Milvus";​
​
// 1. Connect to Milvus server​
ConnectConfig connectConfig = ConnectConfig.builder()​
        .uri(CLUSTER_ENDPOINT)​
        .token(TOKEN)​
        .build();​
​
MilvusClientV2 client = new MilvusClientV2(connectConfig);​
​
// 3. Create a collection in customized setup mode​
​
// 3.1 Create schema​
CreateCollectionReq.CollectionSchema schema = client.createSchema();​
​
// 3.2 Add fields to schema​
schema.addField(AddFieldReq.builder()​
        .fieldName("my_id")​
        .dataType(DataType.Int64)​
        .isPrimaryKey(true)​
        .autoID(false)​
        .build());​
​
schema.addField(AddFieldReq.builder()​
        .fieldName("my_vector")​
        .dataType(DataType.FloatVector)​
        .dimension(5)​
        .build());​
​
schema.addField(AddFieldReq.builder()​
        .fieldName("my_varchar")​
        .dataType(DataType.VarChar)​
        .maxLength(512)​
        .build());​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
import { MilvusClient, DataType } from "@zilliz/milvus2-sdk-node";​
​
const address = "http://localhost:19530";​
const token = "root:Milvus";​
const client = new MilvusClient({address, token});​
​
// 3. Create a collection in customized setup mode​
// 3.1 Define fields​
const fields = [​
    {​
        name: "my_id",​
        data_type: DataType.Int64,​
        is_primary_key: true,​
        auto_id: false​
    },​
    {​
        name: "my_vector",​
        data_type: DataType.FloatVector,​
        dim: 5​
    },​
    {​
        name: "my_varchar",​
        data_type: DataType.VarChar,​
        max_length: 512​
    }​
]​

```

</TabItem>

<TabItem value="Go" label="go">

```Go
import "github.com/milvus-io/milvus/client/v2/entity"​
​
schema := entity.NewSchema().WithDynamicFieldEnabled(true).​
        WithField(entity.NewField().WithName("my_id").WithIsAutoID(true).WithDataType(entity.FieldTypeInt64).WithIsPrimaryKey(true)).​
        WithField(entity.NewField().WithName("my_vector").WithDataType(entity.FieldTypeFloatVector).WithDim(5)).​
        WithField(entity.NewField().WithName("my_varchar").WithDataType(entity.FieldTypeVarChar).WithMaxLength(512))thDim(5))​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
export schema='{​
        "autoId": false,​
        "enabledDynamicField": false,​
        "fields": [​
            {​
                "fieldName": "my_id",​
                "dataType": "Int64",​
                "isPrimary": true​
            },​
            {​
                "fieldName": "my_vector",​
                "dataType": "FloatVector",​
                "elementTypeParams": {​
                    "dim": "5"​
                }​
            },​
            {​
                "fieldName": "my_varchar",​
                "dataType": "VarChar",​
                "elementTypeParams": {​
                    "max_length": 512​
                }​
            }​
        ]​
    }'​

```

</TabItem></Tabs>

## (Optional) Set Index Parameters​{#-optional--set-index-parameters​}

Creating an index on a specific field accelerates the search against this field. An index records the order of entities within a collection. As shown in the following code snippets, you can use `metric_type` and `index_type` to select appropriate ways for Zilliz Cloud to index a field and measure similarities between vector embeddings.​

On Zilliz Cloud, you can use `AUTOINDEX` as the index type for all vector fields, and one of `COSINE`, `L2`, and `IP` as the metric type based on your needs.​

As demonstrated in the above code snippet, you need to set both the index type and metric type for vector fields and only the index type for the scalar fields. Indexes are mandatory for vector fields, and you are advised to create indexes on scalar fields frequently used in filtering conditions.​

For details, refer to [​Indexes](https://zilliverse.feishu.cn/wiki/MTxlwjd0di8ZsxkwSnxcAruPnKh).​

<Tabs><TabItem value="Python" label="python" default>

```Python
# 3.3. Prepare index parameters​
index_params = client.prepare_index_params()​
​
# 3.4. Add indexes​
index_params.add_index(​
    field_name="my_id",​
    index_type="STL_SORT"​
)​
​
index_params.add_index(​
    field_name="my_vector", ​
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
// 3.3 Prepare index parameters​
IndexParam indexParamForIdField = IndexParam.builder()​
        .fieldName("my_id")​
        .indexType(IndexParam.IndexType.STL_SORT)​
        .build();​
​
IndexParam indexParamForVectorField = IndexParam.builder()​
        .fieldName("my_vector")​
        .indexType(IndexParam.IndexType.AUTOINDEX)​
        .metricType(IndexParam.MetricType.COSINE)​
        .build();​
​
List<IndexParam> indexParams = new ArrayList<>();​
indexParams.add(indexParamForIdField);​
indexParams.add(indexParamForVectorField);​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
// 3.2 Prepare index parameters​
const index_params = [{​
    field_name: "my_id",​
    index_type: "STL_SORT"​
},{​
    field_name: "my_vector",​
    index_type: "AUTOINDEX",​
    metric_type: "COSINE"​
}]​

```

</TabItem>

<TabItem value="Go" label="go">

```Go
import (​
    "github.com/milvus-io/milvus/client/v2"​
    "github.com/milvus-io/milvus/client/v2/entity"​
    "github.com/milvus-io/milvus/client/v2/index"​
)​
​
indexOptions := []client.CreateIndexOption{​
    client.NewCreateIndexOption(collectionName, "my_vector", index.NewAutoIndex(entity.COSINE)).WithIndexName("my_vector"),​
    client.NewCreateIndexOption(collectionName, "my_id", index.NewSortedIndex()).WithIndexName("my_id"),​
}​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
export indexParams='[​
        {​
            "fieldName": "my_vector",​
            "metricType": "COSINE",​
            "indexName": "my_vector",​
            "indexType": "AUTOINDEX"​
        },​
        {​
            "fieldName": "my_id",​
            "indexName": "my_id",​
            "indexType": "STL_SORT"​
        }​
    ]'​

```

</TabItem></Tabs>

## Create Collection​{#create-collection​}

If you have created a collection with index parameters, Zilliz Cloud automatically loads the collection upon its creation. In this case, all fields mentioned in the index parameters are indexed.​

The following code snippets demonstrate how to create the collection with index parameters and check its load status.​

<Tabs><TabItem value="Python" label="python" default>

```Python
# 3.5. Create a collection with the index loaded simultaneously​
client.create_collection(​
    collection_name="customized_setup_1",​
    schema=schema,​
    index_params=index_params​
)​
​
res = client.get_load_state(​
    collection_name="customized_setup_1"​
)​
​
print(res)​
​
# Output​
#​
# {​
#     "state": "<LoadState: Loaded>"​
# }​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
import io.milvus.v2.service.collection.request.CreateCollectionReq;​
import io.milvus.v2.service.collection.request.GetLoadStateReq;​
​
// 3.4 Create a collection with schema and index parameters​
CreateCollectionReq customizedSetupReq1 = CreateCollectionReq.builder()​
        .collectionName("customized_setup_1")​
        .collectionSchema(schema)​
        .indexParams(indexParams)​
        .build();​
​
client.createCollection(customizedSetupReq1);​
​
// 3.5 Get load state of the collection​
GetLoadStateReq customSetupLoadStateReq1 = GetLoadStateReq.builder()​
        .collectionName("customized_setup_1")​
        .build();​
​
Boolean loaded = client.getLoadState(customSetupLoadStateReq1);​
System.out.println(loaded);​
​
// Output:​
// true​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
// 3.3 Create a collection with fields and index parameters​
res = await client.createCollection({​
    collection_name: "customized_setup_1",​
    fields: fields,​
    index_params: index_params,​
})​
​
console.log(res.error_code)  ​
​
// Output​
// ​
// Success​
// ​
​
res = await client.getLoadState({​
    collection_name: "customized_setup_1"​
})​
​
console.log(res.state)​
​
// Output​
// ​
// LoadStateLoaded​
// ​

```

</TabItem>

<TabItem value="Go" label="go">

```Go
import "github.com/milvus-io/milvus/client/v2"​
​
err := cli.CreateCollection(ctx, client.NewCreateCollectionOption("customized_setup_1", schema).​
    WithIndexOptions(indexOptions...),​
)​
if err != nil {​
    // handle error​
}​
fmt.Println("collection created")​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
export CLUSTER_ENDPOINT="http://localhost:19530"​
export TOKEN="root:Milvus"​
​
curl --request POST \​
--url "${CLUSTER_ENDPOINT}/v2/vectordb/collections/create" \​
--header "Authorization: Bearer ${TOKEN}" \​
--header "Content-Type: application/json" \​
-d "{​
    \"collectionName\": \"customized_setup_1\",​
    \"schema\": $schema,​
    \"indexParams\": $indexParams​
}"​

```

</TabItem></Tabs>

You can also create a collection without any index parameters and add them afterward. In this case, Zilliz Cloud does not load the collection upon its creation. For details on how to create indexes for an existing collection, refer to [​Index Explained](https://zilliverse.feishu.cn/wiki/JGHqwChJxicxp9kJJf9cgeB2nvg).​

The following code snippet demonstrates how to create a collection without a collection, and the load status of the collection remains unloaded upon creation.​

<Tabs><TabItem value="Python" label="python" default>

```Python
# 3.6. Create a collection and index it separately​
client.create_collection(​
    collection_name="customized_setup_2",​
    schema=schema,​
)​
​
res = client.get_load_state(​
    collection_name="customized_setup_2"​
)​
​
print(res)​
​
# Output​
#​
# {​
#     "state": "<LoadState: NotLoad>"​
# }​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
// 3.6 Create a collection and index it separately​
CreateCollectionReq customizedSetupReq2 = CreateCollectionReq.builder()​
    .collectionName("customized_setup_2")​
    .collectionSchema(schema)​
    .build();​
​
client.createCollection(customizedSetupReq2);​
​
GetLoadStateReq customSetupLoadStateReq2 = GetLoadStateReq.builder()​
        .collectionName("customized_setup_2")​
        .build();​
        ​
Boolean loaded = client.getLoadState(customSetupLoadStateReq2);​
System.out.println(loaded);​
​
// Output:​
// false​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
// 3.4 Create a collection and index it seperately​
res = await client.createCollection({​
    collection_name: "customized_setup_2",​
    fields: fields,​
})​
​
console.log(res.error_code)​
​
// Output​
// ​
// Success​
// ​
​
res = await client.getLoadState({​
    collection_name: "customized_setup_2"​
})​
​
console.log(res.state)​
​
// Output​
// ​
// LoadStateNotLoad​
// ​

```

</TabItem>

<TabItem value="Go" label="go">

```Go
import "github.com/milvus-io/milvus/client/v2"​
​
err := cli.CreateCollection(ctx, client.NewCreateCollectionOption("customized_setup_2", schema))​
if err != nil {​
    // handle error​
}​
fmt.Println("collection created")​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
export CLUSTER_ENDPOINT="http://localhost:19530"​
export TOKEN="root:Milvus"​
​
curl --request POST \​
--url "${CLUSTER_ENDPOINT}/v2/vectordb/collections/create" \​
--header "Authorization: Bearer ${TOKEN}" \​
--header "Content-Type: application/json" \​
-d "{​
    \"collectionName\": \"customized_setup_2\",​
    \"schema\": $schema​
}"​
​
curl --request POST \​
--url "${CLUSTER_ENDPOINT}/v2/vectordb/collections/get_load_state" \​
--header "Authorization: Bearer ${TOKEN}" \​
--header "Content-Type: application/json" \​
-d "{​
    \"collectionName\": \"customized_setup_2\"​
}"​

```

</TabItem></Tabs>

Zilliz Cloud also provides a way for you to create a collection instantly. For details, refer to [​Create Collection Instantly](https://zilliverse.feishu.cn/wiki/BkpkwR8Y1iwxPokhqy0cKY3xn8b).​

## Set Collection Properties​{#set-collection-properties​}

You can set properties for the collection to create to make it fit into your service. The applicable properties are as follows.​

### Set Shard Number​{#set-shard-number​}

Shards are horizontal slices of a collection. Each shard corresponds to a data input channel. Every collection has a shard by default. You can set the appropriate number of shards when creating a collection based on the expected throughput and the volume of the data to insert into the collection.​

In common cases, consider increasing the shard number by one every time the expected throughput increases by 500 MB/s or the volume of data to insert increases by 100 GB. This suggestion does not prevent you from inserting data into the collection using the default shard number.​

The following code snippet demonstrates how to set the shard number when you create a collection.​

<Tabs><TabItem value="Python" label="python" default>

```Python
# With shard number​
client.create_collection(​
    collection_name="customized_setup_3",​
    schema=schema,​
    # highlight-next-line​
    num_shards=1​
)​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
// With shard number​
CreateCollectionReq customizedSetupReq3 = CreateCollectionReq.builder()​
    .collectionName("customized_setup_3")​
    .collectionSchema(collectionSchema)​
    // highlight-next-line​
    .numShards(1)​
    .build();​
client.createCollection(customizedSetupReq3);​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
const createCollectionReq = {​
    collection_name: "customized_setup_3",​
    schema: schema,​
    // highlight-next-line​
    shards_num: 1​
}​

```

</TabItem>

<TabItem value="Go" label="go">

```Go
import "github.com/milvus-io/milvus/client/v2"​
​
err := cli.CreateCollection(ctx, client.NewCreateCollectionOption("customized_setup_3", schema).WithShardNum(1))​
if err != nil {​
    // handle error​
}​
fmt.Println("collection created")​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
export params='{​
    "shardsNum": 1​
}'​
​
export CLUSTER_ENDPOINT="http://localhost:19530"​
export TOKEN="root:Milvus"​
​
curl --request POST \​
--url "${CLUSTER_ENDPOINT}/v2/vectordb/collections/create" \​
--header "Authorization: Bearer ${TOKEN}" \​
--header "Content-Type: application/json" \​
-d "{​
    \"collectionName\": \"customized_setup_3\",​
    \"schema\": $schema,​
    \"params\": $params​
}"​

```

</TabItem></Tabs>

### Enable mmap​{#enable-mmap​}

Zilliz Cloud enables mmap on all collections by default, allowing Zilliz Cloud to map raw field data into memory instead of fully loading them. This reduces memory footprints and increases collection capacity. For details on mmap, refer to [​Use mmap](https://zilliverse.feishu.cn/wiki/AxWmwp8TFiR8tMkUWcZcEJlrnab).​

<Tabs><TabItem value="Python" label="python" default>

```Python
# With mmap​
client.create_collection(​
    collection_name="customized_setup_4",​
    schema=schema,​
    # highlight-next-line​
    enable_mmap=False​
)​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
import io.milvus.param.Constant;​
​
// With MMap​
CreateCollectionReq customizedSetupReq4 = CreateCollectionReq.builder()​
        .collectionName("customized_setup_4")​
        .collectionSchema(schema)​
        // highlight-next-line​
        .property(Constant.MMAP_ENABLED, "false")​
        .build();​
client.createCollection(customizedSetupReq4);​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
client.create_collection({​
    collection_name: "customized_setup_4",​
    schema: schema,​
     properties: {​
        'mmap.enabled': true,​
     },​
})​

```

</TabItem>

<TabItem value="Go" label="go">

```Go
import (​
    "github.com/milvus-io/milvus/client/v2"​
    "github.com/milvus-io/milvus/pkg/common"​
)​
​
err := cli.CreateCollection(ctx, client.NewCreateCollectionOption("customized_setup_4", schema).WithProperty(common.MmapEnabledKey, true))​
if err != nil {​
    // handle error​
}​
fmt.Println("collection created")​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
# REST 暂无此功能。​

```

</TabItem></Tabs>

### Set Collection TTL​{#set-collection-ttl​}

If a collection needs to be dropped for a specific period, consider setting its Time-To-Live (TTL) in seconds. Once the TTL times out, Zilliz Cloud deletes entities in the collection and drops the collection. The deletion is asynchronous, indicating that searches and queries are still possible before the deletion is complete.​

The following code snippet sets the TTL to one day (86400 seconds). You are advised to set the TTL to a couple of days at minimum.​

<Tabs><TabItem value="Python" label="python" default>

```Python
# With TTL​
client.create_collection(​
    collection_name="customized_setup_5",​
    schema=schema,​
    # highlight-start​
    properties={​
        "collection.ttl.seconds": 86400​
    }​
    # highlight-end​
)​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
import io.milvus.param.Constant;​
​
// With TTL​
CreateCollectionReq customizedSetupReq5 = CreateCollectionReq.builder()​
        .collectionName("customized_setup_5")​
        .collectionSchema(schema)​
        // highlight-next-line​
        .property(Constant.TTL_SECONDS, "86400")​
        .build();​
client.createCollection(customizedSetupReq5);​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
const createCollectionReq = {​
    collection_name: "customized_setup_5",​
    schema: schema,​
    // highlight-start​
    properties: {​
        "collection.ttl.seconds": 86400​
    }​
    // highlight-end​
}​

```

</TabItem>

<TabItem value="Go" label="go">

```Go
import (​
    "github.com/milvus-io/milvus/client/v2"​
    "github.com/milvus-io/milvus/pkg/common"​
)​
​
err = cli.CreateCollection(ctx, client.NewCreateCollectionOption("customized_setup_5", schema).​
        WithProperty(common.CollectionTTLConfigKey, 86400)) //  TTL in seconds​
if err != nil {​
        // handle error​
}​
fmt.Println("collection created")​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
export params='{​
    "ttlSeconds": 86400​
}'​
​
export CLUSTER_ENDPOINT="http://localhost:19530"​
export TOKEN="root:Milvus"​
​
curl --request POST \​
--url "${CLUSTER_ENDPOINT}/v2/vectordb/collections/create" \​
--header "Authorization: Bearer ${TOKEN}" \​
--header "Content-Type: application/json" \​
-d "{​
    \"collectionName\": \"customized_setup_5\",​
    \"schema\": $schema,​
    \"params\": $params​
}"​

```

</TabItem></Tabs>

### Set Consistency Level​{#set-consistency-level​}

When creating a collection, you can set the consistency level for searches and queries in the collection. You can also change the consistency level of the collection during a specific search or query.​

<Tabs><TabItem value="Python" label="python" default>

```Python
# With consistency level​
client.create_collection(​
    collection_name="customized_setup_6",​
    schema=schema,​
    # highlight-next​
    consistency_level="Bounded",​
)​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
import io.milvus.v2.common.ConsistencyLevel;​
​
// With consistency level​
CreateCollectionReq customizedSetupReq6 = CreateCollectionReq.builder()​
        .collectionName("customized_setup_6")​
        .collectionSchema(schema)​
        // highlight-next-line​
        .consistencyLevel(ConsistencyLevel.BOUNDED)​
        .build();​
client.createCollection(customizedSetupReq6);​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
const createCollectionReq = {​
    collection_name: "customized_setup_6",​
    schema: schema,​
    // highlight-next​
    consistency_level: "Bounded",​
    // highlight-end​
}​
​
client.createCollection(createCollectionReq);​

```

</TabItem>

<TabItem value="Go" label="go">

```Go
import (​
    "github.com/milvus-io/milvus/client/v2"​
    "github.com/milvus-io/milvus/client/v2/entity"​
)​
​
err := cli.CreateCollection(ctx, client.NewCreateCollectionOption("customized_setup_6", schema).​
    WithConsistencyLevel(entity.ClBounded))​
if err != nil {​
    // handle error​
}​
fmt.Println("collection created")​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
export params='{​
    "consistencyLevel": "Bounded"​
}'​
​
export CLUSTER_ENDPOINT="http://localhost:19530"​
export TOKEN="root:Milvus"​
​
curl --request POST \​
--url "${CLUSTER_ENDPOINT}/v2/vectordb/collections/create" \​
--header "Authorization: Bearer ${TOKEN}" \​
--header "Content-Type: application/json" \​
-d "{​
    \"collectionName\": \"customized_setup_6\",​
    \"schema\": $schema,​
    \"params\": $params​
}"​

```

</TabItem></Tabs>

For more on consistency levels, see [​Consistency Level](https://zilliverse.feishu.cn/wiki/Xx9EwWtekinLZfkWKqic37dDnFb).​

### Enable Dynamic Field​{#enable-dynamic-field​}

The dynamic field in a collection is a reserved JavaScript Object Notation (JSON) field named **$meta**. Once you have enabled this field, Zilliz Cloud saves all non-schema-defined fields carried in each entity and their values as key-value pairs in the reserved field.​

For details on how to use the dynamic field, refer to [​Dynamic Field](https://zilliverse.feishu.cn/wiki/OVxRwZWxNi4pYrkdKxCcOuY2nf1).​


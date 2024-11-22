# Primary Field & AutoID​

The primary field uniquely identifies an entity. This page introduces how to add the primary field of two different data types and how to enable Milvus to automatically allocate primary field values.​

## Overview​{#overview​}

In a collection, the primary key of each entity should be globally unique. When adding the primary field, you need to explicitly set its data type to **VARCHAR** or **INT64**. Setting its data type to **INT64** indicates that the primary keys should be an integer similar to `12345`; Setting its data type to **VARCHAR** indicates that the primary keys should be a string similar to `my_entity_1234`.​

You can also enable **AutoID** to make Milvus automatically allocate primary keys for incoming entities. Once you have enabled **AutoID** in your collection, do not include primary keys when inserting entities.​

The primary field in a collection does not have a default value and cannot be null.​

## Use Int64 Primary Keys​{#use-int64-primary-keys​}

To use primary keys of the Int64 type, you need to set `datatype` to `DataType.INT64` and set `is_primary` to `true`. If you also need Milvus to allocate the primary keys for the incoming entities, also set `auto_id` to `true`.​

<Tabs><TabItem value="Python" label="python" default>

```Python
from pymilvus import MilvusClient, DataType​
​
schema = MilvusClient.create_schema()​
​
schema.add_field(​
    field_name="my_id",​
    datatype=DataType.INT64,​
    # highlight-start​
    is_primary=True,​
    auto_id=True,​
    # highlight-end​
)​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
import io.milvus.v2.common.DataType;​
import io.milvus.v2.service.collection.request.AddFieldReq; ​
import io.milvus.v2.service.collection.request.CreateCollectionReq;​
​
CreateCollectionReq.CollectionSchema schema = client.createSchema();​
​
schema.addField(AddFieldReq.builder()​
        .fieldName("my_id")​
        .dataType(DataType.Int64)​
        // highlight-start​
        .isPrimaryKey(true)​
        .autoID(true)​
        // highlight-end​
        .build());​
);​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
import { DataType } from "@zilliz/milvus2-sdk-node";​
​
const schema = [​
  {​
    name: "pk",​
    description: "ID field",​
    data_type: DataType.VARCHAR,​
    is_primary_key: true,​
    max_length: 100,​
  },​
];​

```

</TabItem>

<TabItem value="Go" label="go">

```Go
// Go 缺失​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
export primaryField='{​
    "fieldName": "my_id",​
    "dataType": "Int64",​
    "isPrimary": true​
}'​
​
export schema="{​
    \"autoID\": true,​
    \"fields\": [​
        $primaryField​
    ]​
}"​

```

</TabItem></Tabs>

## Use VarChar Primary Keys​{#use-varchar-primary-keys​}

To use VarChar primary keys, in addition to changing the value of the `data_type` parameter to `DataType.VARCHAR`, you also need to set the `max_length` parameter for the field. ​

<Tabs><TabItem value="Python" label="python" default>

```Python
schema.add_field(​
    field_name="my_id",​
    datatype=DataType.VARCHAR,​
    # highlight-start​
    is_primary=True,​
    auto_id=True,​
    max_length=512,​
    # highlight-end​
)​

```

</TabItem>

<TabItem value="Java" label="java">

```Java
import io.milvus.v2.common.DataType;​
import io.milvus.v2.service.collection.request.AddFieldReq; ​
​
schema.addField(AddFieldReq.builder()​
        .fieldName("my_id")​
        .dataType(DataType.VarChar)​
        // highlight-start​
        .isPrimaryKey(true)​
        .autoID(true)​
        .maxLength(512)​
        // highlight-end​
        .build());​

```

</TabItem>

<TabItem value="JavaScript" label="Node.js">

```JavaScript
schema.push({​
    name: "my_id",​
    data_type: DataType.VarChar,​
    // highlight-start​
    is_primary_key: true,​
    autoID: true,​
    maxLength: 512​
    // highlight-end​
});​

```

</TabItem>

<TabItem value="Go" label="go">

```Go
// Go 缺失​

```

</TabItem>

<TabItem value="Bash" label="cURL">

```Bash
export primaryField='{​
    "fieldName": "my_id",​
    "dataType": "VarChar",​
    "isPrimary": true​
}'​
​
export schema="{​
    \"autoID\": true,​
    \"fields\": [​
        $primaryField​
    ],​
    \"params\": {​
        \"max_length\": 512​
    }​
}"​

```

</TabItem></Tabs>

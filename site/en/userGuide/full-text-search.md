# Full Text Search​

Full text search is a feature that retrieves documents containing specific terms or phrases in text datasets, then ranking the results based on relevance. This feature overcomes semantic search limitations, which might overlook precise terms, ensuring you receive the most accurate and contextually relevant results. Additionally, it simplifies vector searches by accepting raw text input, automatically converting your text data into sparse embeddings without the need to manually generate vector embeddings.​

Using the BM25 algorithm for relevance scoring, this feature is particularly valuable in retrieval-augmented generation (RAG) scenarios, where it prioritizes documents that closely match specific search terms.​

:::info[📘 Notes​]

By integrating full text search with semantic-based dense vector search, you can enhance the accuracy and relevance of search results. For more information, refer to [​Hybrid Search](https://zilliverse.feishu.cn/wiki/WTsmwWdgOiKnwpkdZdScp093njh).​

:::

## Overview​{#overview​}

Full text search simplifies the process of text-based searching by eliminating the need for manual embedding. This feature operates through the following workflow:​

1. **Text input**: You insert raw text documents or provide query text without any need for manual embedding.​

2. **Text analysis**: Milvus uses an [analyzer](https://zilliverse.feishu.cn/wiki/H8MVwnjdgihp0hkRHHKcjBe9n5e) to tokenize input text into individual, searchable terms.​

3. **Function processing**: The built-in function receives tokenized terms and converts them into sparse vector representations.​

4. **Collection store**: Milvus stores these sparse embeddings in a collection for efficient retrieval.​

5. **BM25 scoring**: During a search, Milvus applies the BM25 algorithm to calculate scores for the stored documents and ranks matched results based on relevance to the query text.​

![X89MdsDSaoHMD9x5i6IcXHE8nse](请手动下载图片并替换)

To use full text search, follow these main steps:​

1. [Create a collection](https://zilliverse.feishu.cn/wiki/RQTRwhOVPiwnwokqr4scAtyfnBf#share-D53HdM2qIos5D5xJGVBcDlDDnyc): Set up a collection with necessary fields and define a function to convert raw text into sparse embeddings.​

2. [Insert data](https://zilliverse.feishu.cn/wiki/RQTRwhOVPiwnwokqr4scAtyfnBf#share-RQcSdp9U9oMosZxPNXhcnH6Rnac): Ingest your raw text documents to the collection.​

3. [Perform searches](https://zilliverse.feishu.cn/wiki/RQTRwhOVPiwnwokqr4scAtyfnBf#share-HIpKdsm9Ro7ZmYxh1oWcwhYRn5g): Use query texts to search through your collection and retrieve relevant results.​

## Create a collection for full text search​{#create-a-collection-for-full-text-search​}

To enable full text search, create a collection with a specific schema. This schema must include three necessary fields:​

- The primary field that uniquely identifies each entity in a collection.​

- A `VARCHAR` field that stores raw text documents, with the `enable_analyzer` attribute set to `True`. This allows Milvus to tokenize text into specific terms for function processing.​

- A `SPARSE_FLOAT_VECTOR` field reserved to store sparse embeddings that Milvus will automatically generate for the `VARCHAR` field.​

### Define the collection schema​{#define-the-collection-schema​}

First, create the schema and add the necessary fields:​

```Python
from pymilvus import MilvusClient, DataType, Function, FunctionType​
​
schema = MilvusClient.create_schema()​
​
schema.add_field(field_name="id", datatype=DataType.INT64, is_primary=True, auto_id=True)​
schema.add_field(field_name="text", datatype=DataType.VARCHAR, max_length=1000, enable_analyzer=True)​
schema.add_field(field_name="sparse", datatype=DataType.SPARSE_FLOAT_VECTOR)​

```

In this configuration,​

- `id`: serves as the primary key and is automatically generated with `auto_id=True`.​

- `text`: stores your raw text data for full text search operations. The data type must be `VARCHAR`, as `VARCHAR` is Milvus' string data type for text storage. Set `enable_analyzer=True` to allow Milvus to tokenize the text. By default, Milvus uses the [`standard analyzer`](https://zilliverse.feishu.cn/wiki/WMSvwXXz4iR7mZkGmUscF3Y1nxs) for text analysis. To configure a different analyzer, refer to [​Overview](https://zilliverse.feishu.cn/wiki/H8MVwnjdgihp0hkRHHKcjBe9n5e).​

- `sparse`: a vector field reserved to store internally generated sparse embeddings for full text search operations. The data type must be `SPARSE_FLOAT_VECTOR`.​

Now, define a function that will convert your text into sparse vector representations and then add it to the schema:​

```Python
bm25_function = Function(​
    name="text_bm25_emb", # Function name​
    input_field_names=["text"], # Name of the VARCHAR field containing raw text data​
    output_field_names=["sparse"], # Name of the SPARSE_FLOAT_VECTOR field reserved to store generated embeddings​
    function_type=FunctionType.BM25,​
)​
​
schema.add_function(bm25_function)​

```

<table data-block-token="EfAfdS3iXoAULPxQ3mwckzTrnUb"><thead><tr><th data-block-token="O3sLd5KNXou4Egxq6XVcoNiJnMW" colspan="1" rowspan="1"><p data-block-token="QRttdgJBpo2hEuxb438c7eOgn2f">Parameter​</p>

</th><th data-block-token="SMGGduN8zo3cgXxVnwZcW0UAnbA" colspan="1" rowspan="1"><p data-block-token="LY39dA2eOoyVUUxvKwlcyyjdn3e">Description​</p>

</th></tr></thead><tbody><tr><td data-block-token="Pbj3dPvuno3x6kxnCsWcTb3knag" colspan="1" rowspan="1"><p data-block-token="EeHOdxCjloFUAGxuY1CcScCTnDe"><code>`name`</code>​</p>

<p data-block-token="FzAJdVbrzozmTdxwy4fcJQkQnlh">​</p>

</td><td data-block-token="VJWydnWHJoV66jx6oEPcH9lGnvh" colspan="1" rowspan="1"><p data-block-token="Clg3dWrJpo39lfxSWjVcbE7GnYm">The name of the function. This function converts your raw text from the <code>`text`</code> field into searchable vectors that will be stored in the <code>`sparse`</code> field.​</p>

</td></tr><tr><td data-block-token="ShPJdlvMQoXnSHxIQ1GcoyegnEb" colspan="1" rowspan="1"><p data-block-token="HFT1dYVCioUj4PxnNSVcYIBInNh"><code>`input_field_names`</code>​</p>

</td><td data-block-token="YiZCdrUaaovWnrxef29cmpQFn9c" colspan="1" rowspan="1"><p data-block-token="YFVOd29cUovDpXx7L2zcJK37n1g">The name of the <code>`VARCHAR`</code> field requiring text-to-sparse-vector conversion. For <code>`FunctionType.BM25`</code>, this parameter accepts only one field name.​</p>

</td></tr><tr><td data-block-token="QpcMdDoXfo62aNxQfoyc2E6lneg" colspan="1" rowspan="1"><p data-block-token="D1LkdH1KIojwKDx14HUcHdDJnPh"><code>`output_field_names`</code>​</p>

</td><td data-block-token="TrvodS2xDoF6UhxeFNScRg86nuf" colspan="1" rowspan="1"><p data-block-token="CO6bdbNhQo9ZprxlGdecjs9RnEf">The name of the field where the internally generated sparse vectors will be stored. For <code>`FunctionType.BM25`</code>, this parameter accepts only one field name.​</p>

</td></tr><tr><td data-block-token="UvgkdWp5RoXa0QxL3CKcoEZVnIf" colspan="1" rowspan="1"><p data-block-token="PWZSd2E48oWB2QxqVoVcMHGxn7c"><code>`function_type`</code>​</p>

</td><td data-block-token="VdcmdmiiWoy0nex8a29clnslnQg" colspan="1" rowspan="1"><p data-block-token="Q2eSdvOqeoNa6dxcGjcc2LKinDg">The type of the function to use. Set the value to <code>`FunctionType.BM25`</code>.​</p>

</td></tr></tbody></table>

:::info[📘 Notes​]

For collections with multiple `VARCHAR` fields requiring text-to-sparse-vector conversion, add separate functions to the collection schema, ensuring each function has a unique name and `output_field_names` value.​

:::

### Configure the index​{#configure-the-index​}

After defining the schema with necessary fields and the built-in function, set up the index for your collection. To simplify this process, use `AUTOINDEX` as the `index_type`, an option that allows Milvus to choose and configure the most suitable index type based on the structure of your data.​

```Python
index_params = MilvusClient.prepare_index_params()​
​
index_params.add_index(​
    field_name="sparse",​
    index_type="AUTOINDEX", ​
    metric_type="BM25"​
)​

```

<table data-block-token="XEoodLxOFoukWJx9aLXcH46snXc"><thead><tr><th data-block-token="PfGNdbuq9o9PEWxzAWecWWoInUf" colspan="1" rowspan="1"><p data-block-token="KX1VdsOJCoO0Exxhg8acsduwncd">Parameter​</p>

</th><th data-block-token="VNwBdAyWKoPktSxYaBtcn5rKnNb" colspan="1" rowspan="1"><p data-block-token="Oo1PduIsxo4HcMx2NRmcxvAMnld">Description​</p>

</th></tr></thead><tbody><tr><td data-block-token="UxxWdkIBPoSbjOx7MO8csiFEn5d" colspan="1" rowspan="1"><p data-block-token="NYODddTbmoYoBrxPQ8ectvGxnPe"><code>`field_name`</code>​</p>

</td><td data-block-token="L2ZGdkB2voKhmsx8ezecoPxmnVf" colspan="1" rowspan="1"><p data-block-token="Y16fdZ6hPoXVlgxSTQjctsTonac">The name of the vector field to index. For full text search, this should be the field that stores the generated sparse vectors. In this example, set the value to <code>`sparse`</code>.​</p>

</td></tr><tr><td data-block-token="Wn1rdzso5o8AmqxqxiqccBpCnD4" colspan="1" rowspan="1"><p data-block-token="WLDrdOzSXoiKEOxoDREctDounRf"><code>`index_type`</code>​</p>

</td><td data-block-token="I9TpdLWlXozM3Hx2Z9mcWvDHnNc" colspan="1" rowspan="1"><p data-block-token="Q3cgdK7OTo3kzXxQ1Y2cSarZned">The type of the index to create. <code>`AUTOINDEX`</code> allows Milvus to automatically optimize index settings. If you need more control over your index settings, you can choose from various index types available for sparse vectors in Milvus. For more information, refer to <a href="https://milvus.io/docs/index.md#Indexes-supported-in-Milvus">[Indexes supported in Milvus](https://milvus.io/docs/index.md#Indexes-supported-in-Milvus)</a>.​</p>

</td></tr><tr><td data-block-token="KJfgdQmD1odMgdxkG6uczBYknQh" colspan="1" rowspan="1"><p data-block-token="XVCsdz9Ulo93A2xavPtcF9Bvnec"><code>`metric_type`</code>​</p>

</td><td data-block-token="S3NHds6MTodtrsxRILIc8E1wngh" colspan="1" rowspan="1"><p data-block-token="G9i7dPczzoyJRHxyXbecrWBBn0d">The value for this parameter must be set to <code>`BM25`</code> specifically for full text search functionality.​</p>

</td></tr></tbody></table>

### Create the collection​{#create-the-collection​}

Now create the collection using the schema and index parameters defined.​

```Python
MilvusClient.create_collection(​
    collection_name='demo', ​
    schema=schema, ​
    index_params=index_params​
)​

```

## Insert text data​{#insert-text-data​}

After setting up your collection and index, you're ready to insert text data. In this process, you need only to provide the raw text. The built-in function we defined earlier automatically generates the corresponding sparse vector for each text entry.​

```Python
MilvusClient.insert('demo', [​
    {'text': 'Artificial intelligence was founded as an academic discipline in 1956.'},​
    {'text': 'Alan Turing was the first person to conduct substantial research in AI.'},​
    {'text': 'Born in Maida Vale, London, Turing was raised in southern England.'},​
])​

```

## Perform full text search​{#perform-full-text-search​}

Once you've inserted data into your collection, you can perform full text searches using raw text queries. Milvus automatically converts your query into a sparse vector and ranks the matched search results using the BM25 algorithm, and then returns the topK (`limit`) results.​

```Python
search_params = {​
    'params': {'drop_ratio_search': 0.6},​
}​
​
MilvusClient.search(​
    collection_name='demo', ​
    data=['Who started AI research?'],​
    anns_field='sparse',​
    limit=3,​
    search_params=search_params​
)​

```

<table data-block-token="M37Zdx7XdoYN41xdKtfcHcJpnqh"><thead><tr><th data-block-token="UhTwdxk3Mo5eLjxff0PcL1CHn8b" colspan="1" rowspan="1"><p data-block-token="OwUXdMhOgoRxjzx5t9ecKR9Zn6J">Parameter​</p>

</th><th data-block-token="GM88dTMzTof30QxS9O2cVyrnnJd" colspan="1" rowspan="1"><p data-block-token="Nlp5dAJY8or40nxV6auc20XHnjh">Description​</p>

</th></tr></thead><tbody><tr><td data-block-token="QpGIdQ2m0oogCvxColKcNWnYnUc" colspan="1" rowspan="1"><p data-block-token="TkffdBxkKo2hVvx9gGucca46nic"><code>`search_params`</code>​</p>

</td><td data-block-token="HYemdqt6Dow9tvxOcYScmYdPn8e" colspan="1" rowspan="1"><p data-block-token="JiIOdJrBcoGIQ4xrqYycMdjnn7g">A dictionary containing search parameters.​</p>

</td></tr><tr><td data-block-token="DJDgdH5WUoZQxkxmLzQcXqcXnQh" colspan="1" rowspan="1"><p data-block-token="LKWbdw498o9mtRxm9gDcg28FnQd"><code>`params.drop_ratio_search`</code>​</p>

</td><td data-block-token="SEJ7d5y18otFTOxy7gLcvLYRnfb" colspan="1" rowspan="1"><p data-block-token="MnladDjOGoUphGxrZzXchD0anzf">Proportion of low-frequency terms to ignore during search. For details, refer to <u><ins>Sparse Vector</ins></u>.​</p>

</td></tr><tr><td data-block-token="XPPYdAYUPoASg5xuIYmcyxqHnPe" colspan="1" rowspan="1"><p data-block-token="T90ndG7H0okLa4xa1wzcHQmEnEg"><code>`data`</code>​</p>

</td><td data-block-token="NMhsduxr1oUESPx2J8YcA8csnA1" colspan="1" rowspan="1"><p data-block-token="ZmEQdkdGtofQsAx9YXNcsnlHnYe">The raw query text.​</p>

</td></tr><tr><td data-block-token="O4OVdL9BIollH1xORz3czhInnSh" colspan="1" rowspan="1"><p data-block-token="CYdGd82dRopaWrxfJ9ycWQQnnPc"><code>`anns_field`</code>​</p>

</td><td data-block-token="MsKIdxGj6oWeBExoFurcxWCnnGh" colspan="1" rowspan="1"><p data-block-token="RsMDdgo0roTSBuxYwm6cGw3inZd">The name of the field that contains internally generated sparse vectors.​</p>

</td></tr><tr><td data-block-token="G0ewd9TQ1o1RQRxZA9ucMO9tnBK" colspan="1" rowspan="1"><p data-block-token="JOyTdUmLIo5aV0x4ChOcLiDQnLh"><code>`limit`</code>​</p>

</td><td data-block-token="H21hdYGZQoQe5FxYnwCch58qn0g" colspan="1" rowspan="1"><p data-block-token="ATKidHgXoo7c7dxM7cgcE46engb">Maximum number of top matches to return.​</p>

</td></tr></tbody></table>

​


# Overview​

In text processing, an **analyzer** is a crucial component that converts raw text into a structured, searchable format. Each analyzer typically consists of two core elements: **tokenizer** and **filter**. Together, they transform input text into tokens, refine these tokens, and prepare them for efficient indexing and retrieval.​

In Milvus, analyzers are configured during collection creation when you add `VARCHAR` fields to the collection schema. Tokens produced by an analyzer can be used to build an index for keyword matching or converted into sparse embeddings for full text search. For more information, refer to [​Keyword Match](https://zilliverse.feishu.cn/wiki/RQQKwqhZUiubFzkHo4WcR62Gnvh) or [​Full Text Search](https://zilliverse.feishu.cn/wiki/RQTRwhOVPiwnwokqr4scAtyfnBf).​

:::info[📘 Notes​]

The use of analyzers may impact performance:​

- **Full text search:** For full text search, DataNode and **QueryNode** channels consume data more slowly because they must wait for tokenization to complete. As a result, newly ingested data takes longer to become available for search.​

- **Keyword match:** For keyword matching, index creation is also slower since tokenization needs to finish before an index can be built.​

:::

## Anatomy of an analyzer​{#anatomy-of-an-analyzer​}

An analyzer in Milvus consists of exactly one **tokenizer** and **zero or more** filters.​

- **Tokenizer**: The tokenizer breaks input text into discrete units called tokens. These tokens could be words or phrases, depending on the tokenizer type.​

- Filters: Filters can be applied to tokens to further refine them, for example, by making them lowercase or removing common words.​

The workflow below shows how an analyzer processes text.​

![HtRmdbJ8hoPNPqxLrG3cA9f6nMf](请手动下载图片并替换)

## Analyzer types​{#analyzer-types​}

Milvus provides two types of analyzers to meet different text processing needs:​

- **Built-in analyzer**: These are predefined configurations that cover common text processing tasks with minimal setup. Built-in analyzers are ideal for general-purpose searches, as they require no complex configuration.​

- **Custom analyzer**: For more advanced requirements, custom analyzers allow you to define your own configuration by specifying both the tokenizer and zero or more filters. This level of customization is especially useful for specialized use cases where precise control over text processing is needed.​

:::info[📘 Notes​]

If you omit analyzer configurations during collection creation, Milvus uses the `standard` analyzer for all text processing by default. For details, refer to [​Standard](https://zilliverse.feishu.cn/wiki/WMSvwXXz4iR7mZkGmUscF3Y1nxs).​

:::

### Built-in analyzer​{#built-in-analyzer​}

Built-in analyzers in Milvus are pre-configured with specific tokenizers and filters, allowing you to use them immediately without needing to define these components yourself. Each built-in analyzer serves as a template that includes a preset tokenizer and filters, with optional parameters for customization.​

For example, to use the `standard` built-in analyzer, simply specify its name `standard` as the `type` and optionally include extra configurations specific to this analyzer type, such as `stop_words`:​

```Python
analyzer_params = {​
    "type": "standard", # Uses the standard built-in analyzer​
    "stop_words": ["a", "an", "for"] # Defines a list of common words (stop words) to exclude from tokenization​
}​

```

The configuration of the `standard` built-in analyzer above is equivalent to setting up a [custom analyzer](https://zilliverse.feishu.cn/wiki/H8MVwnjdgihp0hkRHHKcjBe9n5e#share-N6FndaYZFoIPxExGXTDcEyHgnDc) with the following parameters, where `tokenizer` and `filter` options are explicitly defined to achieve similar functionality:​

```Python
analyzer_params = {​
    "tokenizer": "standard",​
    "filter": [​
        "lowercase",​
        {​
            "type": "stop",​
            "stop_words": ["a", "an", "for"]​
        }​
    ]​
}​

```

Milvus offers the following built-in analyzers, each designed for specific text processing needs:​

- `standard`: Suitable for general-purpose text processing, applying standard tokenization and lowercase filtering.​

- `english`: Optimized for English-language text, with support for English stop words.​

- `chinese`: Specialized for processing Chinese text, including tokenization adapted for Chinese language structures.​

For a list of built-in analyzers and their customizable settings, refer to [​Built-in Analyzer Reference](https://zilliverse.feishu.cn/wiki/VvJowcWXeiFPlDkYU7ScezGznIb).​

### Custom analyzer​{#custom-analyzer​}

For more advanced text processing, custom analyzers in Milvus allow you to build a tailored text-handling pipeline by specifying both a **tokenizer** and filters. This setup is ideal for specialized use cases where precise control is required.​

#### Tokenizer​{#tokenizer​}

The **tokenizer** is a **mandatory** component for a custom analyzer, which initiates the analyzer pipeline by breaking down input text into discrete units or **tokens**. Tokenization follows specific rules, such as splitting by whitespace or punctuation, depending on the tokenizer type. This process allows for more precise and independent handling of each word or phrase.​

For example, a tokenizer would convert text `"Vector Database Built for Scale"` into separate tokens:​

```Plain Text
["Vector", "Database", "Built", "for", "Scale"]​

```

**Example of specifying a tokenizer**:​

```Python
analyzer_params = {​
    "tokenizer": "whitespace",​
}​

```

For a list of tokenizers available to choose from, refer to [​Tokenizer Reference](https://zilliverse.feishu.cn/wiki/Zu6vw6Aifi1gvNkqqO5cDjmtngh).​

#### Filter​{#filter​}

**Filters** are **optional** components working on the tokens produced by the tokenizer, transforming or refining them as needed. For example, after applying a `lowercase` filter to the tokenized terms `["Vector", "Database", "Built", "for", "Scale"]`, the result might be:​

```SQL
["vector", "database", "built", "for", "scale"]​

```

Filters in a custom analyzer can be either **built-in** or **custom**, depending on configuration needs.​

- **Built-in filters**: Pre-configured by Milvus, requiring minimal setup. You can use these filters out-of-the-box by specifying their names. The filters below are built-in for direct use:​

    - `lowercase`: Converts text to lowercase, ensuring case-insensitive matching. For details, refer to [​Lowercase](https://zilliverse.feishu.cn/wiki/AhAhw08MFiB9OpkDjbPcVUTVnlg).​

    - `asciifolding`: Converts non-ASCII characters to ASCII equivalents, simplifying multilingual text handling. For details, refer to [​ASCII folding](https://zilliverse.feishu.cn/wiki/SFLCweOuaiChuVkjazqcqyE7neb).​

    - `alphanumonly`: Retains only alphanumeric characters by removing others. For details, refer to [​Alphanumonly](https://zilliverse.feishu.cn/wiki/BZkiw99tkiDkLXktLhqcJtjKnmb).​

    - `cnalphanumonly`: Removes tokens that contain any characters other than Chinese characters, English letters, or digits. For details, refer to [​Cnalphanumonly](https://zilliverse.feishu.cn/wiki/R3r7wFHUbi8KxUk2t2FcUXoJnic).​

    - `cncharonly`: Removes tokens that contain any non-Chinese characters. For details, refer to [​Cncharonly](https://zilliverse.feishu.cn/wiki/X16rw3C4giUT6bkPLXAcsBapnpe).​

    **Example of using a built-in filter:**​

    ```Python
    analyzer_params = {​
        "tokenizer": "standard", # Mandatory: Specifies tokenizer​
        "filter": ["lowercase"], # Optional: Built-in filter that converts text to lowercase​
    }​

    ```

- **Custom **filters: Custom filters allow for specialized configurations. You can define a custom filter by choosing a valid filter type (`filter.type`) and adding specific settings for each filter type. Examples of filter types that support customization:​

    - `stop`: Removes specified common words by setting a list of stop words (e.g., `"stop_words": ["of", "to"]`). For details, refer to [​Stop](https://zilliverse.feishu.cn/wiki/ScncwBnDBiVoLjksXAwcUgrgnod).​

    - `length`: Excludes tokens based on length criteria, such as setting a maximum token length. For details, refer to [​Length](https://zilliverse.feishu.cn/wiki/MKdvwWBDRi5MMAkkn5PcD1x9nfh).​

    - `stemmer`: Reduces words to their root forms for more flexible matching. For details, refer to [​Stemmer](https://zilliverse.feishu.cn/wiki/JksSwTwJPidjsnk18Olc2TjWnZe).​

    **Example of configuring a custom filter:**​

    ```Python
    analyzer_params = {​
        "tokenizer": "standard", # Mandatory: Specifies tokenizer​
        "filter": [​
            {​
                "type": "stop", # Specifies 'stop' as the filter type​
                "stop_words": ["of", "to"], # Customizes stop words for this filter type​
            }​
        ]​
    }​

    ```

    For a list of available filter types and their specific parameters, refer to [​Filter Reference](https://zilliverse.feishu.cn/wiki/ZIakwn5V8i1msRk7EDscPNqsnUf).​

## Example use​{#example-use​}

In this example, we define a collection schema with a vector field for embeddings and two `VARCHAR` fields for text processing capabilities. Each `VARCHAR` field is configured with its own analyzer settings to handle different processing needs.​

```Python
from pymilvus import MilvusClient, DataType​
​
# Set up a Milvus client​
client = MilvusClient(​
    uri="http://localhost:19530"​
)​
​
# Create schema​
schema = client.create_schema(auto_id=True, enable_dynamic_field=False)​
​
# Add fields to schema​
​
# Use a built-in analyzer​
analyzer_params_built_in = {​
    "type": "english"​
}​
​
# Add VARCHAR field `title_en`​
schema.add_field(​
    field_name='title_en', ​
    datatype=DataType.VARCHAR, ​
    max_length=1000, ​
    enable_analyzer=True，​
    analyzer_params=analyzer_params_built_in,​
    enable_match=True, ​
)​
​
# Configure a custom analyzer​
analyzer_params_custom = {​
    "tokenizer": "standard",​
    "filter": [​
        "lowercase", # Built-in filter​
        {​
            "type": "length", # Custom filter​
            "max": 40​
        },​
        {​
            "type": "stop", # Custom filter​
            "stop_words": ["of", "to"]​
        }​
    ]​
}​
​
# Add VARCHAR field `title`​
schema.add_field(​
    field_name='title', ​
    datatype=DataType.VARCHAR, ​
    max_length=1000, ​
    enable_analyzer=True，​
    analyzer_params=analyzer_params_custom,​
    enable_match=True, ​
)​
​
# Add vector field​
schema.add_field(field_name="embedding", datatype=DataType.FLOAT_VECTOR, dim=3)​
# Add primary field​
schema.add_field(field_name="id", datatype=DataType.INT64, is_primary=True)​
​
# Set up index params for vector field​
index_params = client.prepare_index_params()​
index_params.add_index(field_name="embedding", metric_type="COSINE", index_type="AUTOINDEX")​
​
# Create collection with defined schema​
client.create_collection(​
    collection_name="YOUR_COLLECTION_NAME",​
    schema=schema,​
    index_params=index_params​
)​

```

​


# Standard​

The `standard` tokenizer in Milvus splits text based on spaces and punctuation marks, making it suitable for most languages.​

## Configuration​{#configuration​}

To configure an analyzer using the `standard` tokenizer, set `tokenizer` to `standard` in `analyzer_params`.​

```Python
analyzer_params = {​
    "tokenizer": "standard",​
}​

```

The `standard` tokenizer can work in conjunction with one or more filters. For example, the following code defines an analyzer that uses the `standard` tokenizer and `lowercase` filter:​

```Python
analyzer_params = {​
    "tokenizer": "standard",​
    "filter": ["lowercase"]​
}​

```

:::info[📘 Notes​]

For simpler setup, you may choose to use the [`standard analyzer`](https://zilliverse.feishu.cn/wiki/WMSvwXXz4iR7mZkGmUscF3Y1nxs), which combines the `standard` tokenizer with the [`lowercase filter`](https://zilliverse.feishu.cn/wiki/AhAhw08MFiB9OpkDjbPcVUTVnlg).​

:::

After defining `analyzer_params`, you can apply them to a `VARCHAR` field when defining a collection schema. This allows Milvus to process the text in that field using the specified analyzer for efficient tokenization and filtering. For details, refer to [Example use](https://zilliverse.feishu.cn/wiki/H8MVwnjdgihp0hkRHHKcjBe9n5e#share-I38Md0nO2o1lw2xifGzccPpWncd).​

## Example output​{#example-output​}

Here’s an example of how the `standard` tokenizer processes text:​

**Original text**:​

```Python
"The Milvus vector database is built for scale!"​

```

**Expected output**:​

```Python
["The", "Milvus", "vector", "database", "is", "built", "for", "scale"]​

```
# Jieba​

The `jieba` tokenizer processes Chinese text by breaking it down into its component words.​

## Configuration​{#configuration​}

To configure an analyzer using the `jieba` tokenizer, set `tokenizer` to `jieba` in `analyzer_params`.​

```Python
analyzer_params = {​
    "tokenizer": "jieba",​
}​

```

After defining `analyzer_params`, you can apply them to a `VARCHAR` field when defining a collection schema. This allows Milvus to process the text in that field using the specified analyzer for efficient tokenization and filtering. For details, refer to [Example use](https://zilliverse.feishu.cn/wiki/H8MVwnjdgihp0hkRHHKcjBe9n5e#share-I38Md0nO2o1lw2xifGzccPpWncd).​

## Example output​{#example-output​}

Here’s an example of how the `jieba` tokenizer processes text:​

**Original text**:​

```Python
"Milvus 是一个高性能、可扩展的向量数据库！"​

```

**Expected output**:​

```Python
["Milvus", " ", "是", "一个", "高性", "性能", "高性能", "、", "可", "扩展", "的", "向量", "数据", "据库", "数据库", "！"]​

```
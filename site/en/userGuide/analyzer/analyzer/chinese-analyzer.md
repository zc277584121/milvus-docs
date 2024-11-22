# Chinese​

The `chinese`** **analyzer is designed specifically to handle Chinese text, providing effective segmentation and tokenization.​

### Definition​{#definition​}

The `chinese` analyzer consists of:​

- **Tokenizer**: Uses the `jieba` tokenizer to segment Chinese text into tokens based on vocabulary and context. For more information, refer to [​Jieba](https://zilliverse.feishu.cn/wiki/JGURwBQNOijp2DkspFFctbAGnLh).​

- **Filter**: Uses the `cnalphanumonly` filter to remove tokens that contain any non-Chinese characters. For more information, refer to [​Cnalphanumonly](https://zilliverse.feishu.cn/wiki/R3r7wFHUbi8KxUk2t2FcUXoJnic).​

The functionality of the `chinese` analyzer is equivalent to the following custom analyzer configuration:​

```Python
analyzer_params = {​
    "tokenizer": "jieba",​
    "filter": ["cnalphanumonly"]​
}​

```

### Configuration​{#configuration​}

To apply the `chinese` analyzer to a field, simply set `type` to `chinese` in `analyzer_params`.​

```Python
analyzer_params = {​
    "type": "chinese",​
}​

```

:::info[📘 Notes​]

The `chinese` analyzer does not accept any optional parameters.​

:::

### Example output​{#example-output​}

Here’s how the `chinese` analyzer processes text.​

**Original text**:​

```Python
"Milvus 是一个高性能、可扩展的向量数据库！"​

```

**Expected output**:​

```Python
["Milvus", "是", "一个", "高性", "性能", "高性能", "可", "扩展", "的", "向量", "数据", "据库", "数据库"]​

```

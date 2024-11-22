# English​

The `english` analyzer in Milvus is designed to process English text, applying language-specific rules for tokenization and filtering.​

### Definition​{#definition​}

The `english` analyzer uses the following components:​

- **Tokenizer**: Uses the [`standard tokenizer`](https://zilliverse.feishu.cn/wiki/GAX8wkC1QiTZhXkLBocc1GoTnke) to split text into discrete word units.​

- Filters: Includes multiple filters for comprehensive text processing:​

    - [`lowercase`](https://zilliverse.feishu.cn/wiki/AhAhw08MFiB9OpkDjbPcVUTVnlg): Converts all tokens to lowercase, enabling case-insensitive searches.​

    - [`stemmer`](https://zilliverse.feishu.cn/wiki/JksSwTwJPidjsnk18Olc2TjWnZe): Reduces words to their root form to support broader matching (e.g., "running" becomes "run").​

    - [`stop_words`](https://zilliverse.feishu.cn/wiki/ScncwBnDBiVoLjksXAwcUgrgnod): Removes common English stop words to focus on key terms in text.​

The functionality of the `english` analyzer is equivalent to the following custom analyzer configuration:​

```Python
analyzer_params = {​
    "tokenizer": "standard",​
    "filter": [​
        "lowercase",​
        {​
            "type": "stemmer",​
            "language": "english"​
        }，{​
            "type": "stop",​
            "stop_words": "_english_",​
        }​
    ]​
}​

```

### Configuration​{#configuration​}

To apply the `english` analyzer to a field, simply set `type` to `english` in `analyzer_params`, and include optional parameters as needed.​

```Python
analyzer_params = {​
    "type": "english",​
}​

```

The `english` analyzer accepts the following optional parameters: ​

<table data-block-token="YMmUdQtabozHZnxC09QcajU0nvd"><thead><tr><th data-block-token="N1Qfdbd9Vok7mkx0OGpcx49cnUM" colspan="1" rowspan="1"><p data-block-token="PxYUdGyrMoa4x5x3sCpcF7JLn1e">Parameter​</p>

</th><th data-block-token="WIQKdcE3coxEirxwmpucXGuin7f" colspan="1" rowspan="1"><p data-block-token="VAHCdZFTkoeSJNxgPmicGnOZnWh">Description​</p>

</th></tr></thead><tbody><tr><td data-block-token="NzThd1pxQoektPxhqrQc7Oxcnhl" colspan="1" rowspan="1"><p data-block-token="SW6SdE2iyohhGaxQIfpcjZfCnBx"><code>`stop_words`</code>​</p>

</td><td data-block-token="KSAbdmKPCowsR7x7UO8c8ngFnnh" colspan="1" rowspan="1"><p data-block-token="F3E1dFjL3oUrl5xWq3ucpVPon7c">An array containing a list of stop words, which will be removed from tokenization. Defaults to <code>`_english_`</code>, a built-in set of common English stop words.​</p>

</td></tr></tbody></table>

Example configuration with custom stop words:​

```Python
analyzer_params = {​
    "type": "english",​
    "stop_words": ["a", "an", "the"]​
}​

```

After defining `analyzer_params`, you can apply them to a `VARCHAR` field when defining a collection schema. This allows Milvus to process the text in that field using the specified analyzer for efficient tokenization and filtering. For details, refer to [Example use](https://zilliverse.feishu.cn/wiki/H8MVwnjdgihp0hkRHHKcjBe9n5e#share-I38Md0nO2o1lw2xifGzccPpWncd).​

### Example output​{#example-output​}

Here’s how the `english` analyzer processes text.​

**Original text**:​

```Python
"The Milvus vector database is built for scale!"​

```

**Expected output**:​

```Python
["milvus", "vector", "databas", "built", "scale"]​

```
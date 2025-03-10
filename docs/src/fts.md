# [EXPERIMENTAL] Full text search

LanceDB now provides experimental support for full text search.
This is currently Python only. We plan to push the integration down to Rust in the future
to make this available for JS as well.

## Installation

To use full text search, you must install optional dependency tantivy-py:

# tantivy 0.19.2
pip install tantivy@git+https://github.com/quickwit-oss/tantivy-py#164adc87e1a033117001cf70e38c82a53014d985


## Quickstart

Assume:
1. `table` is a LanceDB Table
2. `text` is the name of the Table column that we want to index

For example,

```python
import lancedb

uri = "data/sample-lancedb"
db = lancedb.connect(uri)

table = db.create_table("my_table",
            data=[{"vector": [3.1, 4.1], "text": "Frodo was a happy puppy"},
                  {"vector": [5.9, 26.5], "text": "There are several kittens playing"}])

```

To create the index:

```python
table.create_fts_index("text")
```

To search:

```python
df = table.search("puppy").limit(10).select(["text"]).to_df()
```

LanceDB automatically looks for an FTS index if the input is str.

## Multiple text columns

If you have multiple columns to index, pass them all as a list to `create_fts_index`:

```python
table.create_fts_index(["text1", "text2"])
```

Note that the search API call does not change - you can search over all indexed columns at once.

## Current limitations

1. Currently we do not yet support incremental writes.
If you add data after fts index creation, it won't be reflected
in search results until you do a full reindex.

2. We currently only support local filesystem paths for the fts index.
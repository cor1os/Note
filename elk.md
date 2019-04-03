# ELK

## elasticsearch

### 查看索引

`curl http://127.0.0.1:9200/_cat/indices?v`

### 删除索引

`curl -XDELETE http://localhost:9200/[INDEX_NAEM]`
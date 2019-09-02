* 版本：https://www.docker.elastic.co
* <server>:9200/index/type/id
* index->database
* type->table
* 不需要预先创建index、type
* type中的数据类型可以不一致
* _search、_mapping
* PUT/POST创建/修改数据，使用POST可省略id
* GET <index>/<type>/_search?q=全文搜索

### 下载sdk
* 打开`https://www.elastic.co/guide/en/elasticsearch/client/community/current/index.html`，选择`go`，选择`elastic`
* 进入`https://github.com/olivere/elastic`，下拉打开`https://gopkg.in/olivere/elastic.v5`，右侧选择版本
*   复制命令`go get -v gopkg.in/olivere/elastic.v6`并执行
*   


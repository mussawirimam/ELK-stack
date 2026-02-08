https://www.elastic.co/docs/api/doc/elasticsearch/operation/operation-indices-put-index-template

### Both Opensearch and ELK are based on the same search engine (Apache Lucene), different distributed wrappers.
<u>Historical note</u>
Elasticsearch used Lucene from day one.
OpenSearch is a fork of Elasticsearch 7.10 → therefore also Lucene-based.
So the answer is unequivocally:
Yes — same core search engine (Lucene), different distributed wrappers.

Here’s the official Apache Lucene documentation page — the authoritative reference from the project itself:
👉 Apache Lucene Release Docs & Documentation:
https://lucene.apache.org/core/documentation.html
On that page you’ll find:
Links to documentation for recent versions of Lucene (e.g., 10.x & 9.x)
Tutorials and “Getting Started” guides
API Javadocs & reference materials
Here’s a direct example of a version-specific docs page:
👉 Apache Lucene 9.11.1 Documentation
https://lucene.apache.org/core/9_11_1/index.html
These pages contain the official manual, guides, and API references for Lucene — the core search library used by systems like Elasticsearch and OpenSearch.

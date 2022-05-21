# Anserini Regressions: BEIR (v1.0.0) &mdash; DBPedia

This page documents BM25 regression experiments for [BEIR (v1.0.0) &mdash; DBPedia](http://beir.ai/).
These experiments index the corpus in a "flat" manner, by concatenating the "title" and "text" into the "contents" field.
All the documents and queries are pre-tokenized with `bert-base-uncased` tokenizer.

The exact configurations for these regressions are stored in [this YAML file](../src/main/resources/regression/beir-v1.0.0-dbpedia-entity-wp.yaml).
Note that this page is automatically generated from [this template](../src/main/resources/docgen/templates/beir-v1.0.0-dbpedia-entity-wp.template) as part of Anserini's regression pipeline, so do not modify this page directly; modify the template instead.

From one of our Waterloo servers (e.g., `orca`), the following command will perform the complete regression, end to end:

```
python src/main/python/run_regression.py --index --verify --search --regression beir-v1.0.0-dbpedia-entity-wp
```

## Indexing

Typical indexing command:

```
target/appassembler/bin/IndexCollection \
  -collection BeirFlatCollection \
  -input /path/to/beir-v1.0.0-dbpedia-entity-wp \
  -index indexes/lucene-index.beir-v1.0.0-dbpedia-entity-wp/ \
  -generator DefaultLuceneDocumentGenerator \
  -threads 1 -storePositions -storeDocvectors -storeRaw -pretokenized \
  >& logs/log.beir-v1.0.0-dbpedia-entity-wp &
```

For additional details, see explanation of [common indexing options](common-indexing-options.md).

## Retrieval

After indexing has completed, you should be able to perform retrieval as follows:

```
target/appassembler/bin/SearchCollection \
  -index indexes/lucene-index.beir-v1.0.0-dbpedia-entity-wp/ \
  -topics src/main/resources/topics-and-qrels/topics.beir-v1.0.0-dbpedia-entity.test.wp.tsv.gz \
  -topicreader TsvString \
  -output runs/run.beir-v1.0.0-dbpedia-entity-wp.bm25.topics.beir-v1.0.0-dbpedia-entity.test.wp.txt \
  -bm25 -removeQuery -pretokenized &
```

Evaluation can be performed using `trec_eval`:

```
tools/eval/trec_eval.9.0.4/trec_eval -c -m ndcg_cut.10 src/main/resources/topics-and-qrels/qrels.beir-v1.0.0-dbpedia-entity.test.txt runs/run.beir-v1.0.0-dbpedia-entity-wp.bm25.topics.beir-v1.0.0-dbpedia-entity.test.wp.txt
tools/eval/trec_eval.9.0.4/trec_eval -c -m recall.100 src/main/resources/topics-and-qrels/qrels.beir-v1.0.0-dbpedia-entity.test.txt runs/run.beir-v1.0.0-dbpedia-entity-wp.bm25.topics.beir-v1.0.0-dbpedia-entity.test.wp.txt
tools/eval/trec_eval.9.0.4/trec_eval -c -m recall.1000 src/main/resources/topics-and-qrels/qrels.beir-v1.0.0-dbpedia-entity.test.txt runs/run.beir-v1.0.0-dbpedia-entity-wp.bm25.topics.beir-v1.0.0-dbpedia-entity.test.wp.txt
```

## Effectiveness

With the above commands, you should be able to reproduce the following results:

| nDCG@10                                                                                                      | BM25      |
|:-------------------------------------------------------------------------------------------------------------|-----------|
| BEIR (v1.0.0): DBPedia                                                                                       | 0.2841    |


| R@100                                                                                                        | BM25      |
|:-------------------------------------------------------------------------------------------------------------|-----------|
| BEIR (v1.0.0): DBPedia                                                                                       | 0.4255    |


| R@1000                                                                                                       | BM25      |
|:-------------------------------------------------------------------------------------------------------------|-----------|
| BEIR (v1.0.0): DBPedia                                                                                       | 0.6124    |
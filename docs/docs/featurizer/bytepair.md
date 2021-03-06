This featurizer is a *dense* featurizer. If you're interested in learning how these
work you might appreciate reading[the original article](http://www.lrec-conf.org/proceedings/lrec2018/pdf/1049.pdf).
Recognition should be given to Benjamin Heinzerling and Michael Strube for making these available.

A main feature of these types of embeddings is that they are relatively lightweight but also
their availability in **many** languages. BytePair embeddings exist for 277 languages that are
pretrained on wikipedia. There's also availability for a multi-language setting.

More information can be found [here](https://nlp.h-its.org/bpemb/). When you scroll down you will
notice a list of languages that are available. Here's some links for a detailed view of available
vectors:

- [English](https://nlp.h-its.org/bpemb/en/)
- [Zulu](https://nlp.h-its.org/bpemb/zu/)
- [Esperanto](https://nlp.h-its.org/bpemb/eo/)
- [Afrikaans](https://nlp.h-its.org/bpemb/af/)
- [Hungarian](https://nlp.h-its.org/bpemb/hu/)
- [Multi Language](https://nlp.h-its.org/bpemb/multi/)

## Configurable Variables

- **lang**: specifies the lanuage that you'll use, default = "en"
- **dim**: specifies the dimension of the subword embeddings, default = 25,
- **vs**: specifies the vocabulary size of the segmentation model, default = 1000,
- **vs_fallback**: if set to True and the given vocabulary size can't be loaded for the given model, the closest size is chosen, default=True,
- **cache_dir**: specifies the folder in which downloaded BPEmb files will be cached, default = `~/.cache/bpemb`
- **model_file**: specifies the path to a custom model file, default=None,
- **emb_file**: specifies the path to a custom embedding file, default=None

## Base Usage

The configuration file below demonstrates how you might use the BytePair embeddings. In this example
we're not using any cached folders and the library will automatically download the correct embeddings
for you and save them in `~/.cache`. Both the embeddings as well as a model file will be saved.

```yaml
language: en

pipeline:
- name: WhitespaceTokenizer
- name: LexicalSyntacticFeaturizer
- name: CountVectorsFeaturizer
  analyzer: char_wb
  min_ngram: 1
  max_ngram: 4
- name: rasa_nlu_examples.featurizers.dense.BytePairFeaturizer
  lang: en
  vs: 1000
  dim: 25
- name: DIETClassifier
  epochs: 100

policies:
  - name: MemoizationPolicy
  - name: KerasPolicy
  - name: MappingPolicy
```

## Cached Usage

If you're using pre-downloaded embedding files (in docker you might have this on a mounted disk)
then you can prevent a download from happening. We'll be doing that in the example below.

```yaml
language: en

pipeline:
- name: WhitespaceTokenizer
- name: LexicalSyntacticFeaturizer
- name: CountVectorsFeaturizer
  analyzer: char_wb
  min_ngram: 1
  max_ngram: 4
- name: rasa_nlu_examples.featurizers.dense.BytePairFeaturizer
  lang: en
  vs: 10000
  dim: 100
  cache_dir: "tests/data"
- name: DIETClassifier
  epochs: 100

policies:
  - name: MemoizationPolicy
  - name: KerasPolicy
  - name: MappingPolicy
```

Note that in this case we expect two files to be present in the `tests/data` directory;

- `en.wiki.bpe.vs10000.d100.w2v.bin`
- `en.wiki.bpe.vs10000.model`

You can also overwrite the names of these files via the `model_file` and `emb_file` settings. But it
is preferable to stick to the library naming convention.

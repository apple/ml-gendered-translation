# Generating Gender Alternatives in Machine Translation


This repository contains datasets used in the paper, [Generating Gender Alternatives in Machine Translation](https://arxiv.org/abs/2407.20438)

Machine translation (MT) systems often translate terms with ambiguous gender (e.g., English term `the nurse`) into the gendered form that is most prevalent in the systems’ training data (e.g., `enfermera`, the Spanish term for a female nurse). This often reflects and perpetuates harmful stereotypes present in society. In this work, we train MT models which can identify such ambiguities and produce grammatically correct translations (gender alternatives) corresponding to any combination of masculine/feminine gender assignments for ambiguous entities. This repository open sources the training and test datasets used in the work.

## Documentation

`data/` folder contains test datasets for 6 language pairs: `English-Spanish (en-es), English-German (en-de), English-French (en-fr), English-Russian (en-ru), English-Portuguese (en-pt), English-Italian (en-it)` and train datasets for 5 language pairs: `en-es, en-de, en-fr, en-ru, en-pt`

### Test datasets
`data/test/raw/` contains the human annotated test datasets. The English source sentences in files `gate.*` are taken from [GATE](https://github.com/MicrosoftTranslator/GATE) and in files `geneval.*` from [MT-GenEval](https://github.com/amazon-science/machine-translation-gender-eval). The translations are taken from the above sources if available else manually annotated. Gender annotations are then placed on top of the source sentences and the translations. Each tsv file contains the following columns:

1. `id`: row/example number
2. `source`: English source sentence with potential gender ambiguous entities
3. `invalid`: Whether the sentence was marked invalid by annotators due to the sentence not making sense or containing offensive/vulgar language
4. `source with entity referring words`: English source sentence, with each word referring to an entity which can have natural gender enclosed within `[[ ]]`
5. `entity referring words grouped`: Words enclosed in `All references` which refer to the same entity are grouped together. For e.g. if `All references` contain `A [[carpenter]] [[friend]] of [[mine]].`, `[[carpenter]] (index 0)` and `[[friend]] (index 1)` refer to the same entity, whereas `[[mine]] (index 2)` refers to a different entity. `References grouped` should thus contain the grouping `[0,1],[2]`, containing 2 groups (each group referring to a unique entity).
6. `headword in each group`: For each group, the index of the word which most acts as the "head-word" or "subject". In the above example, if the annotators judged `[[carpenter]] (index 0)` and `[[mine]] (index 2)` as head words of the two groups, this column should contain `0,2`
7. `gender labels of headwords`: Gender of each group as inferred from just the grammar of the source sentence. Genders are marked one of `Amb (ambiguous)`, `Male (masculine)` or `Female (feminine)`.
8. `source with headwords`: Source sentence, but only the headwords for all the groups are enclosed within `[[]]`
9. `vanilla translation`: Vanilla translation for the source sentence not containing any information about gender alternatives
10. `translation with gender structures`: Vanilla translation augmented with gender structures wherever needed
11. `gender alignments`: A mapping from all the headwords to all the gender structures. It contains a comma separated list of `x-y`, where `x` is the index (zero-indexed position) of the headword and `y` is the index of the gender structure.

For more details about the annotation process, please refer to our paper.

`data/test/processed/` contains the processed testsets derived from the raw annotations. `test.<src_lang>-<tgt_lang>.<src_lang>` contains the source sentences for the `<src_lang>-<tgt_lang>`language pair from both GATE and MT-GenEval. `test.<src_lang>-<tgt_lang>.<tgt_lang>` contains the translations corresponding to the source sentences. If the source sentence does not contain any ambiguous entity, then exactly one translation is present. If the source sentence contains gender ambiguous entities which can affect the translation, then this file contains 2 translations (tab separated), the first translation is corresponding to all gender ambiguous entities being masculine and the second translation is corresponding to all gender ambiguous entities being feminine.
The `Alternatives Metrics`, `Delta-BLEU` and `Structure Metrics` from the paper are computed over these processed testsets.

### Train datasets

`data/train/raw/` contains the training datasets G-Trans and G-Tag. These datasets are synthetically generated and expected to contain some amount of noise.

G-Trans dataset (`gtrans.<src_lang>-<tgt_lang>.tsv`) has the following fields: `id, source with headwords, gender labels of headwords, translation with gender structures, gender alignments`. These fields are described above in detail. Each example is guaranteed to have atleast one ambiguous gendered entity and atleast one gender structure.

G-Tag dataset (`gtag.<src_lang>-<tgt_lang>.tsv`) has the following fields: `id, source with headwords, gender labels of headwords`.

For more details about the source sentences contained in the G-Trans/G-Tag dataset, please refer to our paper.

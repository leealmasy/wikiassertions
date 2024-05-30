---
dataset_info:
  features:
  - name: articleId
    dtype: int64
  - name: lineId
    dtype: int64
  - name: factId
    dtype: int64
  - name: text
    dtype: string
  - name: subj
    dtype: string
  - name: pred
    dtype: string
  - name: auxi
    sequence: string
  - name: prep1
    dtype: string
  - name: obj1
    dtype: string
  - name: prep2
    dtype: string
  - name: obj2
    dtype: string
  - name: prep3
    dtype: string
  - name: obj3
    dtype: string
  - name: prep4
    dtype: string
  - name: obj4
    dtype: string
  - name: prep5
    dtype: string
  - name: obj5
    dtype: string
  splits:
  - name: train
    num_bytes: 86532750060
    num_examples: 439305160
  download_size: 31248975339
  dataset_size: 86532750060
configs:
- config_name: default
  data_files:
  - split: train
    path: data/train-*
---

# WikiAssertions: A Dataset of Assertions from Wikipedia

WikiAssertions contains all the Assertions (a.k.a. Atomic Facts) in Wikipedia. 
It was created by running a [strong multi-valent open IE system](https://github.com/Teddy-Li/MulVOIEL/) on the sentencized [Wikipedia](https://huggingface.co/datasets/wikipedia) corpus.
The same exact model checkpoint that we used to parse the corpus can be downloaded at [Teddy487/LLaMA3-8b-for-OpenIE](https://huggingface.co/Teddy487/LLaMA3-8b-for-OpenIE).

Assertions are multi-valent relation tuples representing factoid information at the atomic level. 

For example, given the following sentence:

`Earlier this year , President Bush made a final `` take - it - or - leave it '' offer on the minimum wage`

The following assertion can be extracted:

<<span style="color:#2471A3">President Bush</span>, <span style="color:#A93226">made</span>, <span style="color:#138D75">a final "take-it-or-leave-it" offer</span>, <span style="color:#B7950B ">on the minimum wage</span>, <span style="color:#B9770E">earlier this year</span>>

We introduce the data format below, and refer users to our [Github Repository](https://github.com/Teddy-Li/MulVOIEL/) and our Model Cards ([Teddy487/LLaMA2-7b-for-OpenIE](https://huggingface.co/Teddy487/LLaMA2-7b-for-OpenIE), [**Teddy487/LLaMA3-8b-for-OpenIE**](https://huggingface.co/Teddy487/LLaMA3-8b-for-OpenIE)) for more information.

## Data Format

Each entry in this dataset is an assertion in Wikipedia. An assertion is a multi-valent relation tuple of the format: 

`<subj> ,, (<auxi> ###) <predicate> ,, (<prep1> ###) <obj1>, (<prep2> ###) <obj2>, ...`

An assertion includes a subject, a predicate (essentially verbs), an optional auxiliary (negation / modal verb / etc.), and a number of objects (0, 1, 2, etc.).
Each object may come with an optional preposition (e.g. on, with, for, etc.).

The dataset follows the format laid out as belows:

### MetaData Columns:

1. articleId: the ID of the document as in the id column in the [Wikipedia](https://huggingface.co/datasets/wikipedia) corpus.
2. lineId: the sentence ID within a document as sentencized using the [spaCy sentencizer](https://spacy.io/api/sentencizer).
3. factId: the assertion ID within the sentence.

### Content Columns:

1. text: the assertion presented in the form of natural language text.
2. subj: subject of the assertion.
3. pred: predicate (main verb) of the assertion.
4. (optional) auxi: auxiliary element of the assertion (negation, model verbs, etc.)
5. (optional) prep1: preposition for object 1 (can be empty);
6. (optional) obj1: object 1 (typically the direct object for transitive verbs, could be empty for intranitive verbs).
7. (optional) prep2: preposition for object 2 (can be empty);
8. (optional) obj2: object 2
10. (optional) prep3: preposition for object 3 (can be empty);
11. (optional) obj3: object 3
12. (optional) prep4:preposition for object 4 (can be empty);
13. (optional) obj4: object 4
14. (optional) prep5:preposition for object 5 (can be empty);
15. (optional) obj5: object 5

Note that we keep a maximum number of 5 object slots per assertion. When an assertion involves more arguments, the overflowing arguments are ignored.

When the predicate is a [light verb](https://en.wikipedia.org/wiki/Light_verb), the light verb itself does not bear sufficient meaning to disambiguate the eventualities.
Therefore, in that case, we merge the first object (`obj1`) into the `pred` field. 
We only do this when the first object does not come with a preposition (i.e. `prep1` is empty); otherwise, we treat it as an anomaly and disgard that assertion.


## Mapping with the original text in Wikipedia

The original text for each assertion in the dataset can be found from the [Wikipedia](https://huggingface.co/datasets/wikipedia) corpus in Huggingface.
We use the `20220301.en` version of Wikipedia, which can be loaded using the following python command:

```python
from datasets import load_dataset

wiki_corpus = load_dataset("wikipedia", "20220301.en", split='train')
```

The `articleId` field in the dataset corresponds to the document `id` in the loaded dataset.

If you wish to locate the exact sentence from which an assertion was extracted, you can use the following python commands:

```python
import spacy

nlp = spacy.load('en_core_web_sm')
nlp.add_pipe("sentencizer")
nlp.select_pipes(enable=["sentencizer"])

doc = wiki_corpus[articleId]['text']
doc = nlp(doc)
sents = list(doc.sents)

this_sent = sents[lineId].text

print(this_sent)
```

Note that you would first need to run `python -m spacy download en_core_web_sm` before running the above script.




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

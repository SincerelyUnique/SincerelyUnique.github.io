---
title: 使用Universal Sentence Encoder检测文本相似度
date: 2020-09-16 17:30:41
tags:
- nlp
- tensorflow
categories:
---

```python
# Universal Sentence Encoder
# From： Cornell University
 
# install:
# pip install tensorflow
# pip install tensorflow_hub
 
# ref: https://stackoverflow.com/questions/8897593/how-to-compute-the-similarity-between-two-text-documents
 
import tensorflow.compat.v1 as tf
import tensorflow_hub as hub
import numpy as np
import matplotlib.pyplot as plt
 
# module_url = "https://tfhub.dev/google/universal-sentence-encoder/1?tf-hub-format=compressed"
 
# Import the Universal Sentence Encoder's TF Hub module
tf.compat.v1.disable_eager_execution()
embed = hub.Module(r"D:\nlp\model")
 
 
def convert_text_2_dot_vector(messages):
    similarity_input_placeholder = tf.placeholder(tf.string, shape=None)
    similarity_message_encodings = embed(similarity_input_placeholder)
    with tf.Session() as session:
        session.run(tf.global_variables_initializer())
        session.run(tf.tables_initializer())
        message_embeddings_ = session.run(similarity_message_encodings,
                                          feed_dict={similarity_input_placeholder: messages})
        corr = np.inner(message_embeddings_, message_embeddings_)
        print(corr)
        return corr
 
 
def heatmap(x_labels, y_labels, values):
    fig, ax = plt.subplots()
    im = ax.imshow(values)
 
    # We want to show all ticks...
    ax.set_xticks(np.arange(len(x_labels)))
    ax.set_yticks(np.arange(len(y_labels)))
    # ... and label them with the respective list entries
    ax.set_xticklabels(x_labels)
    ax.set_yticklabels(y_labels)
 
    # Rotate the tick labels and set their alignment.
    plt.setp(ax.get_xticklabels(), rotation=45, ha="right", fontsize=10, rotation_mode="anchor")
 
    # Loop over data dimensions and create text annotations.
    for i in range(len(y_labels)):
        for j in range(len(x_labels)):
            text = ax.text(j, i, "%.2f"%values[i, j],
                           ha="center", va="center", color="w", fontsize=6)
    fig.tight_layout()
    plt.show()
 
 
if __name__ == '__main__':
    # sample text
    messages = [
        # Smartphones
        "My phone is not good.",
        "Your cellphone looks great.",
 
        # Weather
        "Will it snow tomorrow?",
        "Recently a lot of hurricanes have hit the US",
 
        # Food and health
        "An apple a day, keeps the doctors away",
        "Eating strawberries is healthy",
    ]
    dot_vec = convert_text_2_dot_vector(messages)
    heatmap(messages, messages, dot_vec)
```

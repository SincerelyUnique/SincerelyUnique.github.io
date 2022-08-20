---
title: 使用spacy检测文本相似度
date: 2020-09-16 15:54:02
tags:
- nlp
- spacy
categories:
- [学习, Python语言学习, NLP]
---

```python
# 这个比我们自己通过余弦相似度计算更精确一些，可以基本达到语义相似匹配
# This looks like it's the formula for computing cosine similarity and the vectors seem to be created with SpaCy's .vector which the documentation says is trained from GloVe's w2v model.
 
# github: https://github.com/explosion/spaCy
# doc： https://spacy.io/usage/vectors-similarity
# install: pip install spacy
# install english model: python -m spacy download en_core_web_sm[md|lg], ref: https://spacy.io/models/en
# install chinese model: python -m spacy download zh_core_web_sm[md|lg], ref: https://spacy.io/models/zh
 
import spacy
 
 
def check_word_property():
    nlp = spacy.load("en_core_web_sm")
    input_sentence = input('Please input a sentence: ')
    while input_sentence:
        doc = nlp(input_sentence)
        word_property = [{'word': w.text, 'property': w.pos_} for w in doc]
        noun_list = [wp['word'] for wp in word_property if wp['property'] == 'NOUN']
        verb_list = [wp['word'] for wp in word_property if wp['property'] == 'VERB']
        print('Input sentence: %s' % input_sentence)
        print('Noun list: %s' % ', '.join(noun_list))
        print('Verb list: %s\n' % ', '.join(verb_list))
        input_sentence = input('Please input a sentence: ')
 
 
def check_en_word_similarity():
    # 为了使比较算法简洁和快速，spaCy的小模型(所有以sm结尾的包)都不使用单词向量，
    # 而且这些sm包只包含上下文相关的向量，我们虽然可以使用similarity()方法，但结果不会太理想
    # 所以为了使用真正的词向量，最好使用en_core_web_lg模型
    nlp = spacy.load('en_core_web_lg')
    tokens = nlp('dog cat banana ssafsf')  # ssafsf会被警告改单词没有向量
 
    # 内置单词向量的模型使它们成为可用的标记,文本向量将默认为它们的token向量的平均值,
    # 通过has_vector我们可以检查一个token是否有分配的向量，并得到L2规范，它可以用来使向量标准化
    print('Token属性：\n')
    for token in tokens:
        print(token.text, token.has_vector, token.vector_norm, token.is_oov)
 
    print('\nToken两两之间关系：\n')
    for token1 in tokens:
        for token2 in tokens:
            print('%s and %s: %s' % (token1, token2, token1.similarity(token2)))
 
 
def check_en_doc_similarity():
    nlp = spacy.load('en_core_web_lg')
    doc1 = nlp('How to create ticket?')
    doc2 = nlp('How can I create ticket?')
    doc3 = nlp('I want to create ticket?')
    doc4 = nlp('Do you know how to create the ticket?')
    print(doc1.similarity(doc2))  # 0.9540794124760449
    print(doc2.similarity(doc3))  # 0.9751696134778753
    print(doc1.similarity(doc3))  # 0.9549075548119442
    print(doc1.similarity(doc4))  # 0.9547450107860063
 
    sentence3 = nlp('Do you know I love you?')
    sentence4 = nlp('I love you so much?')
    print(sentence3.similarity(sentence4))  # 0.9629862471369796
 
 
def check_zh_doc_similarity():
    nlp = spacy.load('zh_core_web_lg')
    doc1 = nlp('你好吗?')
    doc2 = nlp('你还好吗?')
    doc3 = nlp('今天你还好吗?')
    doc4 = nlp('你的身体今天还好吗?')
    print(doc1.similarity(doc2))  # 0.7544851165307768
    print(doc2.similarity(doc3))  # 0.9664107589955437
    print(doc1.similarity(doc3))  # 0.730822854943996
    print(doc1.similarity(doc4))  # 0.6528684500574182
 
 
if __name__ == '__main__':
    check_word_property()
 
    check_en_word_similarity()
 
    check_zh_doc_similarity()
    check_en_doc_similarity()
 
    print('end!')
```

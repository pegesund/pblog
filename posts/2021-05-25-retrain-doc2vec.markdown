---
title: Retraining a doc2vec-model 
tags: doc2vec, word2vec, python 
---

Word2vec represented the big breakthrough in using word embeddings and the importance of this model in the nlp-domain is hard to overestimate. The followup model with paragrah embeddings was also important, but these days more sophisticated models like Bert or InferSent has taken the focus in the news. Reason for this is off course the introduction of the attention concept in the neural network models - this was a innovation which has been as important for the nlp-domain as convolution has been for the image recognizion.

But in in some areas I think the paragraph embeddings are still pretty interesting. We have been looking at how to use Doc2vec from Gensim to improve a traditional search in our company. The fact that documents and words are trained in the same vector space gives the possibility to move between these entities. One can find relevant words around a doc, as well as new word to word connections. In Doc2Vec this gives pretty good results, while in Bert for example the "meaning" of a word is so connected to its context that it does not make sense to talk about a word in itself.

One challenge in Doc2Vec has been that it does not support retraining. This is a drawback, in our case we would like to start off with a pretrained word vocabulary based on wikipedia and then add domain specific documents on top of this. Retraining with all the material at once takes many non-needed resourses. First of all the training with the wiki-content takes several days on a large computer - and we also end up with all the wiki embeddings taking lots (!) of ram in the end model.

Therefore we have experimented with retraining, based on an existing vocabulary. This has already been "fixed" once as a part of a phd - codebase and paper is found [here](https://github.com/redyandri/doc2vec-master). The problem is that this branch was based on the 3.2 version of Gensim and Gensim has moved to version 4 in the meanwhile, with lots of fixes and improvements. In the meantime the problem has appeard regularly on the gensim mailing lists, but without any solution we could use. 

Our attempt looks promsing and I have not found any issues with it so far, but it is takes some extra code. It is also not well integrated into Gensim as we have to run the cleanups outside the Gensim-codebase (maybe we transforms this to complete patch later). This is tested on Gensim 4.1 and might (probably) not work on later releases.

Code is below, if anyone else runs into the same problem. It reads in a pretrained wiki-model and then adds a new docs on top of this. It verifies that the new doc was placed at a relevent position in the vector space and that the new word introduced in the second training round also looks sane.

The crucial lines for retraining are below the "Create temporay doc2vec" and "Clean variables.." comments.


```python
import gensim
import numpy as np
from gensim.models.doc2vec import Doc2Vec
from gensim.models.doc2vec import Doc2Vec, TaggedDocument
from nltk.tokenize import word_tokenize


class DocIterator:
    def __init__(self, prefix):
        self.num = 0 
        self.prefix = prefix

    def __iter__(self):
        return self

    def __next__(self):
        num = self.num
        prefix = self.prefix
        if self.num < len(data):
            self.num += 1
            return TaggedDocument(words=word_tokenize(data[num].lower()), tags=[prefix + str(num)]) 
        self.num = 0
        raise StopIteration

model2 = Doc2Vec.load('/var/tmp/wiki/lang-nb_no-prodution_test_20210521001818_lemmatized.model')
data = [
        "jav python ruby xxyfg",
        "jav python ruby xxyfg",
        "jav python ruby xxyfg",
        "jav python ruby xxyfg",
        "jav python ruby xxyfg",
        "jav python ruby xxyfg",
        "jav python ruby xxyfg",
        "jav python ruby xxyfg",
        "jav python ruby xxyfg",
        "fotball keeper"
        ]

# Create temporary doc2vec
tagged_data = DocIterator("E") 
model_new = Doc2Vec()
model_new.build_vocab(tagged_data)
model2.build_vocab(tagged_data, update=True, keep_raw_vocab=True)
model2.index_to_key = model_new.dv.index_to_key 
model2.key_to_index = model_new.dv.key_to_index
model2.dv.vectors = model_new.dv.vectors

# now train
model2.train(tagged_data, total_examples=model2.corpus_count, epochs=250)
print("Retrained")

# clean variables after retrain
deleteDocs = []
for docName, docIndex in model2.dv.key_to_index.items():
    if not docName in model2.dv.index_to_key:
      deleteDocs.append(docName) 

print("Deleting docs: ", len(deleteDocs))
for docName in deleteDocs:
    del model2.dv.key_to_index[docName]

print("Fixed")

# verify no crash
model2.save('/var/tmp/model2')
model3 = Doc2Vec.load('/var/tmp/model2')

# these two should be pretty similar
print("----")
e0 = model2.dv['E0']
print(model3.wv.most_similar([e0], topn=10))
temp_doc_vector = model3.infer_vector(['jav', 'python', 'ruby', 'xxyfg'], epochs=150)
print(model3.wv.most_similar([temp_doc_vector], topn=10))
print("----")
print(model3.wv.most_similar([model3.wv['xxyfg']], topn=10))
print(model2.dv.index_to_key)
print(model2.dv.key_to_index)

```


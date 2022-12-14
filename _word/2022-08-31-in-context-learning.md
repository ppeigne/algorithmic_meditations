---
title: "What is 'in-context learning'?"
description: "An exploration of the concept of in-context learning"
categories: [in-context-learning, CHERI]
toc: false
layout: post
comments: true
image: https://upload.wikimedia.org/wikipedia/commons/c/cd/Giulio_Rosati_10.jpg
---
![]({{ site.baseurl }}/images/Copy%20of%20CHERI_LogoV1.png){: style="float: left"} ***Acknowledgements:***<br>*This work is part of the result of my [CHERI Summer Research Project](https://effectivealtruism.ch/2022-summer-research-program).<br>Many thanks to my mentor [Asa Cooper Stickland](https://homepages.inf.ed.ac.uk/s1302760/) for his advices on this project,<br>and to the CHERI for giving me this opportunity to do my first AI Safety research in such a nice environment!*

# Key takeaways
- In-context learning can be defined as the property that, **given a prompt of text**, **later tokens are easier to predict than earlier ones**.
- This property requieres the **presence of novelty in the run-time environment** to be observed.
- The novelty can be introduced in the run-time environment using orthogonal strategies:
  - from the introduction of **new entities**.
  - from the introduction of a **new distribution of the known entities**.

![The discussion, Guilio Rosati]({{ site.baseurl }}/images/conversation.png)

# Definition

### Tokens
### Context

## Broad definition
Discovered with GPT2, in-context learning is a key emergent property of Large Language Models. In-context learning can be defined as the property that, **given a prompt of text**, **later tokens are easier to predict than earlier ones**. 

For instance, **the model’s prediction of what follows [hog] will dramatically increase toward [wart] if it is prompted with an extract of the Harry Potter novel** (assuming the prompted text contains a mention of the Hogwart school of wizardry), **even though the model was trained on a textual corpus that never mentioned the Harry Potter universe** (e.g. Shakespeare poems). 

![]({{ site.baseurl }}/images/Screenshot from 2022-08-31 14-34-51.png)

In other words, our model will learn to write in the style of J.K. Rowling from its prompt while having been only trained to imitate Shakespeare. 

This phenomenon is therefore called “in-context learning” because the information from context (i.e. the previous part of the prompt) can be used by the model to improve its prediction. **The longer the context the better the prediction.**

## Novelty
On its most basic level, in-context learning is defined (for a model being given a text prompt as input) as the property of being able to produce a better prediction for the later tokens of the prompt than for the earlier ones. This improvement is a result of the model being able to use the information present in the prompt (also called the context), i.e. "to learn from context".

This ability implies that **the task from the context cannot be performed well only relying on the model’s previous learning**. 

However, starting from this definition it is possible to produce different interpretations of what "learning from context" means. To make sure the model does not rely on learning from its training to perform the task, **novelty must be injected into the prompted task**. 

Orthogonal interpretations rely on a different approach to how to bring novelty into the context:
- **Novelty of the entities**: the context's entities are unknown to the model (i.e. never been encountered in the pre-training phase).
- **Novelty of the tokens distribution**: the model is given an unusual task based on an unusual distribution of tokens. 


## 'New entities based' in-context learning (Chan et al. 2022)
The gist of this approach is that asking the model to adapt to **completely new entities** (i.e. new tokens) implies that **it cannot rely on what it has learned before** to perform the task. This task can then be considered “new” for the model.

#### Example: the incomplete (but consistent) English-Korean mapping
Imagine an experiment where some english characters are systematically replaced by the closest sounding Korean characters: `[n] -> [ㄴ]`, `[k] -> [ㅋ]`, `[o] -> [ㅗ]`, etc. 
- The replacement is consistent: each english character is always replaced by the same korean character.
- The replacement is incomplete: not every english character are replaced.

After the replacement, the modified text is used as a prompt for the model trained on the Shakespear corpus. The model is expected to output text in the style of Shakespeare **but** with the same English-Korean mapping it got as an input.

| Training data | Context data |
|-|-|
|![](https://cdn-icons-png.flaticon.com/128/2723/2723896.png)<br>*"To be or not to be..."*|![]({{ site.baseurl }}/images/korean_shakespeare.png)[^1]<br>*"To be ㅗr ㄴㅗt to be..."*<br>**New entities**|


## 'New Distribution-based' in-context learning (Xie et al. 2022)

The gist of this approach is that asking the model to adapt to a **specific distribution of tokens different enough** from its training data distribution that it **cannot rely on what it has learned before** to perform the task. This task can then be considered as “new” for the model. 

The example of Harry Potter is an example of a new distribution of tokens. The entities are the same, but their distribution is different.  
For instance, the token [wart] already exists in the Shakespeare vocabulary but is not used after [hog]. Based on the training data distribution, the probability P([wart] | …[hog]) is close to zero.

| Training data | Context data |
|-|-|
|![](https://cdn-icons-png.flaticon.com/128/2723/2723896.png)<br>*"To be or not to be..."*|![](https://cdn-icons-png.flaticon.com/128/1600/1600953.png)<br>*“Don't let the muggles get you down.”*<br>**New distribution**|



| | New entities | Same entities |
|-|-|-|
|**New distribution** |![](https://cdn-icons-png.flaticon.com/128/5789/5789238.png)<br>*"잠의 들판으로"*[^2]|![](https://cdn-icons-png.flaticon.com/128/1600/1600953.png)<br>*“Don't let the muggles get you down.”*|
|**Same distribution**|![]({{ site.baseurl }}images/korean_shakespeare.png)<br>*"To be ㅗr ㄴㅗt to be..."*|![](https://cdn-icons-png.flaticon.com/128/2723/2723896.png)<br>*"To be or not to be..."*|





**Is the in-context learning based on the same data distribution (task) as the pre-training phase?**


**Is in-context learning based on the same entities as the training phase?**

- Known: Yes. The distribution of entities among the data can change but none of them are completely new to the model. 
For instance, the token [wart] already exists in the Shakespeare vocabulary but is not used after [hog]: based on the training data distribution, the probability P([wart] | …[hog]) is close to zero. 

- New:  No. The entities are completely new to the model.
For instance, the model trained on Shakespeare’s poems gets fed in Korean characters sequences to complete.



Example from Chan et al. (2022):
The model is trained to map images to labels (hand-written letters with a corresponding numerical label). Then the model is asked to predict the label of images unseen during the training (but given in the context). 

||Training|In-prompt|
|-|-|-|
|**Known data: Xie et al. (2022)**|The model is trained to predict the next token of a document, based on **a fixed vocabulary**. <br><br> *“Marie Salomea Skłodowska–Curie; born Maria Salomea Skłodowska, 7 November 1867 – 4 July 1934) was a Polish and naturalized-French physicist and chemist who conducted pioneering research on radioactivity. She was the first woman to win a Nobel Prize…”*|The model is asked to complete (i.e. predict the next token) a prompt having a very unusual distribution of entities (i.e. a task different from the original training task) from **the same known vocabulary.** <br><br> *“Albert Einstein was German<br> Mahatma Gandhi was Indian <br> Marie Curie was”*|
|**New data: Chan et al. (2022)**|The model is trained on image-labels pairs to predict the label of the last image. <br> ![]({{ site.baseurl }}/images//Screenshot%20from%202022-08-31%2014-21-15.png)|The model is prompted with a sequence of **totally new image-label pairs** to predict the label of the last image. <br> ![]({{ site.baseurl }}/images//Screenshot%20from%202022-08-31%2014-29-49.png)|


### New distribution / 'Distribution-based' in-context learning (Xie et al. 2022)
**Is the in-context learning based on the same data distribution (task) as the pre-training phase?**


| Training data | Context data |
|-|-|
|![](https://cdn-icons-png.flaticon.com/128/2723/2723896.png) |![](https://cdn-icons-png.flaticon.com/128/2353/2353710.png)|


| | New entities | Same entities |
|-|-|-|
|**New distribution** ||![](https://cdn-icons-png.flaticon.com/128/2353/2353710.png)|
|**Same distribution**|![]({{ site.baseurl }}images/korean_shakespeare.png)|![](https://cdn-icons-png.flaticon.com/128/2723/2723896.png)|



- Same: Yes. The distribution of data is the same, hence the task is the same.
For instance, the model gets fed in with the beginning of an unknown (i.e. not present in the training data) Shakespeare’s poem to complete. The distribution of tokens is supposed to be very similar to what the model was trained with. 

- Unusual: No. The task is somewhat different from the pre-training task because the distribution of tokens is different.
For example, the model trained on Shakespeare gets fed in with an extract of a Harry Potter novel to complete. If the distribution of tokens is different from the training data, then the task has changed. 

The gist of this approach is that asking the model to adapt to a specific distribution of tokens different enough from its training data distribution implies that it cannot rely on what it has learned before to perform the task. This task can then be considered as “new” for the model. 

Example from Xie et al. (2022): 
Training a model on data from Wikipedia will lead the model to learn a distribution of tokens that are linked together following specific statistics. A Wikipedia article is usually focused mainly on one subject, says Marie Curie, over an entire document composed of dozens, or even hundreds of sentences.  

On the other hand, the in-prompt task will produce a completely different distribution to follow.  
*“Albert Einstein was German  
Mahatma Gandhi was Indian  
Marie Curie was”*  
-> *“Polish”.*

In such a setting, the distribution shifts very quickly from one subject (Albert Einstein, Mahatma Gandhi, Marie Curie) to another in a way that is very unlikely (but not impossible) to appear in the training data.

More formally, we could say that P(“Mahatma” \| “Albert Einstein was German”) is very low given the model’s training distribution.


||Training|In-prompt|
|-|-|-|
|**Unusual distribution: Xie et al. (2022)**|The model is trained to predict the next token of a document, based on **a fixed vocabulary**. <br><br> *“Marie Salomea Skłodowska–Curie; born Maria Salomea Skłodowska, 7 November 1867 – 4 July 1934) was a Polish and naturalized-French physicist and chemist who conducted pioneering research on radioactivity. She was the first woman to win a Nobel Prize…”*|The model is asked to complete (i.e. predict the next token) a prompt having a **very unusual distribution of entities** (i.e. a task different from the original training task) from the same known vocabulary. <br><br> *“Albert Einstein was German<br> Mahatma Gandhi was Indian <br> Marie Curie was”*|
|**Same distribution: Chan et al. (2022)**|The model is trained on image-labels pairs to predict the label of the last image. <br> ![]({{ site.baseurl }}/images/Screenshot%20from%202022-08-31%2014-21-15.png)|The model is prompted with a sequence of totally new image-label pairs to predict the label of the last image. <br> ![]({{ site.baseurl }}/images/Screenshot%20from%202022-08-31%2014-29-49.png)|

### Mapping novelty
We can connect those orthogonal dimensions to evaluate claims about in-context learning. 
This bi-dimensional classification also highlights possible experimental setups combining novelty of the distribution (task) and of the entities, which have not been investigated yet.

||New Entities|Known Entities|
|-|-|-|
|**Unusual distribution**|**Not explored yet**<br>Training:<br> *“Marie Salomea Skłodowska–Curie; born Maria Salomea Skłodowska, 7 November 1867 – 4 July 1934) was a Polish and naturalized-French physicist and chemist who conducted pioneering research on radioactivity. She was the first woman to win a Nobel Prize…”* <br>Prompt:<br> *“Albert Einstein was German<br> [new_token1] [new_token2] was [new_token3] <br> Marie Curie was”*|Xie et al. (2022) <br>**Classic in-prompt instructions**<br>Training:<br> *“Marie Salomea Skłodowska–Curie; born Maria Salomea Skłodowska, 7 November 1867 – 4 July 1934) was a Polish and naturalized-French physicist and chemist who conducted pioneering research on radioactivity. She was the first woman to win a Nobel Prize…”* <br>Prompt:<br> *“Albert Einstein was German<br> Mahatma Gandhi was Indian <br> Marie Curie was”*|
|**Same distribution**|Chan et al. (2022)<br> Training:<br> ![]({{ site.baseurl }}/images/Screenshot%20from%202022-08-31%2014-21-15.png)<br> Prompt:<br> ![]({{ site.baseurl }}/images/Screenshot%20from%202022-08-31%2014-29-49.png)|No novelty|


## References


## Footnotes
[^1] This is Shakespeare with a korean hat, not [Guy Fawkes](https://en.wikipedia.org/wiki/Guy_Fawkes).
[^2] Korean poem, ["Toward the Field of the Sleep"](https://www.poetrytranslation.org/poems/towards-the-field-of-sleep)


# Definition
### Terminology alert!
The building blocks of natural language processing are not words, but **tokens**. A token is a **sequence of characters from which words are composed**. **The set of all tokens is the vocabulary** of the model. The training of the model will produce a specific vocabulary based on the type of text it is trained with.  
*For example, the word “emergence” could be divided into [em][er][ge][nce] (or any other combination of characters that is most useful to the model).*




## A simple definition





## Concurent explanations
Until being properly explained, emergent behaviors point by definition to weaknesses in the theoretical framework. Surprising events are surprising only to the extent that they are not clearly understood.

The emergence of in-prompt learning is still not well understood and subject to concurrent hypotheses. 

For instance (not being comprehensive): 
- Xie et al. (2022) claim that Transformers are the only architecture able to display in-context learning, while Chan et al. (2022) experiment on a synthetic dataset pretends to make in-prompt learning feasible for both Transformers and LSTMs. Both papers also mentioned datasets' specific properties to make in-prompt learning happen.  

- Anthropic’s team in its Transformer Circuit thread proposed an approach based on a mechanistic interpretation of transformers models which led to the discovery of ‘induction heads’. According to the theoretical framework, they are developing, these elements are supposed to be core components leading to in-context learning abilities.

- Another approach suggests that the transformers' impressive abilities (including in-context learning) could come from saturation of their parameters during their training. 

## Narrower definitions of in-context learning




---
layout: project_page
permalink: /
title: "Coarse-to-Fine Highlighting: Reducing Knowledge Hallucination in Large Language Models"
authors:
    <span class="author-block">Qitan Lv, Jie Wang, Hanzhu Chen, Bin Li, Yongdong Zhang, Feng Wu</span>
affiliations:
    University of Science and Technology of China
paper: static/COFT.pdf
code: https://github.com/shiliu-egg/ICML2024_COFT
---
<div align="center">
  <h1>Performance Overview</h1>
</div>

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="static/image/coft_fig.png" width = "100%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      Figure 1. COFT achieves state-of-the-art performance on a broad range of long-form tasks compared with existing methods, using ChatGPT as the backbone.
  	</div>
</center>

<br>

> **TL; DR:** We introduce **COFT** (COarse-to-Fine highlighTing) to focus on different granularity-level key texts, thereby avoiding getting lost in lengthy contexts and reducing knowledge hallucination in large language models. Our COFT:
>
> - 🔥 **Over 30% improvement** in F1 score on the knowledge hallucination benchmark [FELM](https://arxiv.org/abs/2310.00741)
> - 🔥 Effectively alleviates the [Lost in the Middle](https://arxiv.org/abs/2307.03172) issue within LLMs.
> - 🔥 Exhibits remarkable versatility across various long-form tasks, such as reading comprehension and question answering.

<br>



<div align="center">
  <h1>Abstract</h1>
</div>
Generation of plausible but incorrect factual information, often termed hallucination, has attracted
significant research interest. Retrieval-augmented
language model (RALM)—which enhances models with up-to-date knowledge—emerges as a
promising method to reduce hallucination. However, existing RALMs may instead exacerbate hallucination when retrieving lengthy contexts. To
address this challenge, we propose COFT, a novel
            <b>CO</b>arse-to-<b>F</b>ine highligh<b>T</b>ing method to focus
on different granularity-level key texts, thereby
avoiding getting lost in lengthy contexts. Specifically, COFT consists of three components: recaller, scorer, and selector. First, recaller applies
a knowledge graph to extract potential key entities in a given context. Second, scorer measures
the importance of each entity by calculating its
contextual weight. Finally, selector selects high
contextual weight entities with a dynamic threshold algorithm and highlights the corresponding
paragraphs, sentences, or words in a coarse-to-fine manner. Extensive experiments on the knowledge hallucination benchmark demonstrate the
effectiveness of COFT, leading to a superior performance over 30% in the F1 score metric. Moreover, COFT also exhibits remarkable versatility
across various long-form tasks, such as reading
comprehension and question answering.

<div align="center">
  <h1>Motivation</h1>
</div>

Even the currently most capable LLM still exhibits knowledge hallucination issues, i.e., GPT4 may also generate plausible yet incorrect factual information. Moreover, in longform tasks consisting of multiple sentences or paragraphs, hallucination can be exacerbated.

This results in a dilemma inherent in existing RALM systems:

<ul>
  <li><b>The lack of complete contextual semantics.</b> When only retrieving several relevant sentences, the lack of complete contextual semantics may lead to misunderstandings.</li>
  <li><b>The lost in the long context.</b> When retrieving the entire document for comprehensive information, irrelevant texts also distract their reasoning.</li>
</ul>

We thus propose a COarse-to-Fine highlighTing method (COFT) that <b>promotes LLMs to focus on key lexical units, preserving complete contextual semantics and avoiding getting lost in long contexts.</b> COFT highlights different granularity-level lexical units in a coarse-to-fine manner, such as paragraphs, sentences, and words.

<div align="center">
  <h1>Overview of COFT</h1>
</div>

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="static/image/COFT_Overview.png" width = "100%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      Figure 2. An overview of COFT. COFT integrates recaller, scorer, and selector into a unified framework to reduce knowledge hallucination. The workflow is as follows. (1) Perform Named Entity Recognition on the query to extract potential candidate entities. (2) Search the neighboring entities for each potential entity in the knowledge graph to enrich the candidates. (3) Retain candidates that are also present in the reference context as the final key entities. (4) Calculate the contextual weight for each key entity. (5) Calculate the threshold to filter a dynamic proportion of entities. (6) Choose the granularity for highlighting, such as word, sentence, or paragraph. (7) Highlight the reference context based on filtered entities and selected granularity.
  	</div>
</center>

<br>
COFT organically integrates three modules in a unified framework.

<div align="center">
  <h2>Recaller</h2>
</div>

Recaller integrates an external open-source knowledge graph (KG), wikidata, to extract potential key entities as candidates within the query and reference context. To enrich the candidates, recaller also retrieves their one-hop neighbors from the KG. <b>The objective of recaller is to identify potential key entities.</b>

The workflow of Recaller is as follows:

(i) Recaller first conducts named entity recognition on the query to extract named entities that represent keywords within the query. These entities include specific terms and important nouns such as people, places, organizations, etc.

(ii) After obtaining named entities, Recaller leverages them to search one-hop neighbor entities in Wikidata to enrich candidate entities. The named entities and corresponding one-hop neighbors are combined to form candidate entities for the query.

(iii) Recaller finally retains candidate entities that are also present in the reference context, forming the final candidate key entities list.

Given a query such as "Which country or city has the maximum number of nuclear power plants?", recaller **first** performs named entity recognition to identify entities like "country", "city", and "nuclear power plants". **Then**, recaller extracts one-hop neighboring entities from wikidata for each named entity, such as "United States" and "France". **Finally**, based on these named entities and neighboring entities, recaller retains entities that are present in the reference context as the final candidate key entities list. For example, "France" will not be retained because it is not in the reference context.

<br>

<div align="center">
  <h2>Scorer</h2>
</div>

After obtaining candidate key entities, scorer proceeds to assess their importance. With this desiderata, scorer proposes an entity-level iterative algorithm based on a small language model, Llama 7B to calculate the contextual weight of each entity in the context. Entities with higher contextual weights indicate a stronger correlation with the query, and vice versa. **Scorer assigns different weights to measure the importance of each entity.** Algorithm 1 outlines the overall procedure.

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="static/image/scorer.png" width="100%" alt="Scorer Algorithm"/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
    </div>
</center>

<p>Specifically, we first segment reference contexts <b>x<sup>refs</sup></b> into a sentence list <b>ℒ</b> = [<b>s</b><sub>1</sub>, <b>s</b><sub>2</sub>, <b>s</b><sub>3</sub>, &hellip;]. Drawing upon the TF-IDF (<b>T</b>erm <b>F</b>requency–<b>I</b>nverse <b>D</b>ocument <b>F</b>requency) algorithm, a well-suited text relevance assessment and text mining approach that enables the exclusion of the majority of common entities while preserving important entities. We introduce the TF-I<u>S</u>F algorithm, which involves considering the TF-IDF algorithm at the <u><b>S</b></u>entence level. For a given entity <b>e<sub>k</sub></b> in sentence <b>s<sub>i</sub></b>, the corresponding TF-ISF calculation function is as follows:</p>

<div style="text-align: center;">
    <p style="margin: 0;">
        <strong>TF–ISF(<b>e<sub>k</sub></b>) = <sup>f<sub>e<sub>k</sub>, s<sub>i</sub></sub></sup> / |<b>s<sub>i</sub></b>| &times; log<sub>2</sub>(|<b>ℒ</b>| / (f<sub>e<sub>k</sub>, ℒ</sub> + 1))</strong>
    </p>
</div>

<p>where <b>f<sub>e<sub>k</sub>, s<sub>i</sub></sub></b> and <b>f<sub>e<sub>k</sub>, ℒ</sub></b> denote the number of times <b>e<sub>k</sub></b> appears in <b>s<sub>i</sub></b> and <b>ℒ</b>. <b>|s<sub>i</sub>|</b> and <b>|ℒ|</b> denote the number of words within sentence <b>s<sub>i</sub></b> and reference contexts <b>ℒ</b>.</p>

<p>TF-ISF evaluates the importance of entities in reference contexts based on word frequency and effectively distinguishes common but unimportant entities. Higher TF-ISF suggests that the entity plays a more important role in understanding the sentence semantics, and vice versa.</p>

<p>We further concatenate the query and reference context to measure the importance of each token in the reference context based on self-information. Given the input <b>x<sup>que</sup></b> ⊕ <b>x<sup>refs</sup></b>, the self-information calculation function is as follows:</p>

<div style="text-align: center;">
    <p style="margin: 0;">
        <strong>I(<b>t<sub>i</sub></b>) = -log<sub>2</sub> P(<b>t<sub>i</sub></b> | <b>x<sup>que</sup></b>, <b>t<sub>1</sub></b>, <b>t<sub>2</sub></b>, &hellip;, <b>t<sub>i-1</sub></b>)</strong>
    </p>
</div>

<p>where <b>t<sub>i</sub></b> denotes the <i>i</i>-th token within the reference context <b>x<sup>refs</sup></b>, <b>P(t<sub>i</sub> | x<sup>que</sup>, t<sub>1</sub>, t<sub>2</sub>, &hellip;, t<sub>i-1</sub>)</b> denotes its output probability by the small language model <b>ℳ<sub>s</sub></b>, and <b>I(t<sub>i</sub>)</b> denotes the self-information of token <b>t<sub>i</sub></b>. We can further leverage the additivity property of self-information to merge tokens into entity <b>e</b>, thereby obtaining the self-information of each individual key candidate entity <b>I(e)</b>.</p>

<p>To comprehensively consider both the TF-ISF and self-information, we propose contextual weights to indicate the importance of each key candidate entity in the reference context. A higher contextual weight suggests greater importance of the entity to answer the query. The contextual weight calculation function is as follows:</p>

<div style="text-align: center;">
    <p style="margin: 0;">
        <strong>w(<b>e<sub>k</sub></b>) = TF–ISF(<b>e<sub>k</sub></b>) &times; I(<b>e<sub>k</sub></b>)</strong>
    </p>
</div>

<p>where <b>TF–ISF(e<sub>k</sub>)</b> and <b>I(e<sub>k</sub>)</b> denote the TF-ISF and self-information of a key candidate entity <b>e<sub>k</sub></b>, respectively. Other combination methods for TF-ISF and self-information scores are also feasible, and we leave it as a future work.</p>


<div align="center">
  <h2>Selector</h2>
</div>

Selector proposes a dynamic threshold algorithm that considers both the length and informativeness of reference contexts to select high contextual weight entities.Selector then highlights each context based on these entities in a coarse-to-fine manner. **Selector selects the final key entities and highlights the reference context.**

<p>After obtaining candidate key entities and their contextual weights, <i>selector</i> highlights the final lexical units for the query. Specifically, <i>selector</i> first sorts entities based on contextual weights, and proposes a dynamic threshold algorithm to filter a dynamic proportion of key entities. The dynamic thresholds can be defined as <b>&#964;</b> = 0.5 &times; (<b>&#964;<sub>len</sub></b> + <b>&#964;<sub>info</sub></b>), where <b>&#964;<sub>len</sub></b> and <b>&#964;<sub>info</sub></b> denote the min-max normalized value of the length and informativeness for each reference context.</p>

<p><b>&#964;</b> varies with the length and informativeness of the reference context, as longer and more informative reference contexts require more highlights. Then, <i>selector</i> highlights the reference context according to the granularity of selected lexical units. This highlighting process is as follows:</p>

<ol type="i">
    <li>Split the reference context according to the granularity of selected lexical units.</li>
    <li>Calculate the contextual weight of the split lexical units by summing the contextual weight of candidate key entities that occur in the split.</li>
    <li>Sort these lexical units in descending order by their contextual weight, and select the lexical units with contextual weights in the top <b>&#964;</b> &times; 100% for highlighting.</li>
</ol>

<p>After selecting the highlighted lexical units, <i>selector</i> inserts special symbols around these lexical units. Considering the rich diversity of formatting found in publicly accessible web data, which forms a part of the pre-training corpus for LLMs, we adopt markdown syntax, particularly the <b>bold syntax</b> <b>(**)</b> as an example, to highlight important lexical units. This approach aligns with the natural occurrence of formatted text in online sources, thereby enabling the LLMs to more accurately interpret and process textual emphasis as it appears in real-world scenarios.</p>

<p>Take word-level granularity highlighting as an example. If the selected highlighted entities are "nuclear power plants" and "United States", then the sentence "The nuclear power plants in the United States play a crucial role in providing &#8230;" will be highlighted as "The <b>**nuclear power plants**</b> in the <b>**United States**</b> play a crucial role in providing &#8230;" as input for LLM inference. Other highlighting methods, such as HTML bold symbols or different markdown syntax, are also viable options and we leave the exploration as a future work.</p>

<br>


<div class="columns is-centered has-text-centered">
    <div class="column is-four-fifths">
        <h2>Citation</h2>
        <div class="content has-text-justified">
        </div>
    </div>
</div>

If you find our work useful your research, please cite our paper:

```
@inproceedings{lvcoarse,
  title={Coarse-to-Fine Highlighting: Reducing Knowledge Hallucination in Large Language Models},
  author={Lv, Qitan and Wang, Jie and Chen, Hanzhu and Li, Bin and Zhang, Yongdong and Wu, Feng},
  booktitle={Forty-first International Conference on Machine Learning}
}
```

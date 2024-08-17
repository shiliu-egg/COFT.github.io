---
layout: project_page
permalink: /
title: Coarse-to-Fine Highlighting: Reducing Knowledge Hallucination in Large Language Models
authors:
    <span class="author-block">Qitan Lv<sup style="color:#6fbf73;">1</sup><sup>,</sup><sup style="color:#ffac33;">2</sup><sup>,</sup><sup style="color:#007bff;">3</sup><sup>*,</sup>,</span>
    <span class="author-block">Jie Wang<sup style="color:#ffac33;">2</sup>,</span>
    <span class="author-block">Hanzhu Chen<sup style="color:#6fbf73;">1</sup>,</span>
    <span class="author-block">Bin Li<sup style="color:#ffac33;">2</sup>,</span>
    <span class="author-block">Yongdong Zhang<sup style="color:#ffac33;">2</sup>,</span>
    <span class="author-block">Feng Wu<sup style="color:#ffac33;">2</sup>,</span>
affiliations:
    <sup style="color:#6fbf73;">1</sup>University of Science and Technology of China <br>
paper: static/COFT.pdf
code: https://github.com/shiliu-egg/ICML2024_COFT
---

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

<div class="columns is-centered has-text-centered">
    <div class="column is-four-fifths">
        <h2>Abstract</h2>
        <div class="content has-text-justified">
Generation of plausible but incorrect factual information, often termed hallucination, has attracted
significant research interest. Retrieval-augmented
language model (RALM)—which enhances models with up-to-date knowledge—emerges as a
promising method to reduce hallucination. However, existing RALMs may instead exacerbate hallucination when retrieving lengthy contexts. To
address this challenge, we propose COFT, a novel
            <b>CO</b>b>arse-to-<b>F</b>ine highligh<b>T</b>ing method to focus
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
        </div>
    </div>
</div>

<div class="columns is-centered has-text-centered">
    <div class="column is-four-fifths">
        <h2>Overview of COFT</h2>
        <div class="content has-text-justified">
        </div>
    </div>
</div>


Our PEARL framework consists of a draft model, a target model and two strategies to decode tokens. The two strategies are switched according to the verification results in the last decoding step.

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

<div class="columns is-centered has-text-centered">
    <div class="column is-four-fifths">
        <h2>Appendix: Details of implementations of COFT</h2>
        <div class="content has-text-justified">
        </div>
    </div>
</div>


We illustrate the whole algorithm of COFT with Algorithm 1.

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="static/image/scorer.png" width = "100%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
  	</div>
</center>

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

---
layout: project_page
permalink: /

title: Parallel Speculative Decoding with Adaptive Draft Length
authors:
    <span class="author-block">Tianyu Liu<sup style="color:#6fbf73;">1</sup><sup>,</sup><sup style="color:#ffac33;">2</sup><sup>,</sup><sup style="color:#007bff;">3</sup><sup>*,</sup>,</span>
    <span class="author-block">Yun Li<sup style="color:#ffac33;">2</sup>,</span>
    <span class="author-block">Qitan Lv<sup style="color:#6fbf73;">1</sup>,</span>
    <span class="author-block">Kai Liu<sup style="color:#ffac33;">2</sup>,</span>
    <span class="author-block">Jianchen Zhu<sup style="color:#ffac33;">2</sup>,</span>
    <span class="author-block">Winston Hu<sup style="color:#ffac33;">2</sup>,</span>
affiliations:
    <sup style="color:#6fbf73;">1</sup>University of Science and Technology of China <br>
    <sup style="color:#ffac33;">2</sup>Tencent <br>
    <sup style="color:#007bff;">3</sup>OpenGVLab, Shanghai AI Laboratory <br>
    <sup>*</sup> This work is done when Tianyu Liu works as an intern in Tencent
paper: static/PEARL.pdf
code: https://github.com/smart-lty/ParallelSpeculativeDecoding
---

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="https://s2.loli.net/2024/08/13/u3tc4FAwxQG126y.png" width = "100%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      Figure 1. Speedup on HumanEval. All the experiments are conducted with H100 80G GPUs. The part results of <a href="https://github.com/thunlp/Ouroboros">Ouroboros</a> and <a href="https://github.com/hao-ai-lab/LookaheadDecoding">Lookahead Decoding</a> are reproduced with their official codes.
  	</div>
</center>


<br>

> TL; DR: we introduce **PEARL** (Parallel spEculative decoding with Adaptive dRaft Length) to further reduce the inference latency of Large Language Models (LLMs). PEARL is a **parallel** inference framework based on [speculative decoding](https://arxiv.org/abs/2211.17192) which utilizes *pre-verify* and *post-verify* to achieve adaptive draft length. In summary, our PEARL is:
>
> - &#128293; up to **3.87**$\times$, **3.81**$\times$, **3.59**$\times$ and **3.95**$\times$ on HumanEval, GSM8K, MT-bench and MGSM, respectively.
> - **provably lossless**
> - **training-free**, and does not need additional memory
> - &#128293; can be applied to any algorithms based on draft-then-verify framework, such as [EAGLE](https://sites.google.com/view/eagle-llm) and [Medusa](https://sites.google.com/view/medusa-llm)
> - &#128293; Eliminating the burden of searching the optimal draft length, together with a larger expectation of accepted tokens.

<br>

<!-- Using HTML to center the abstract -->

<div class="columns is-centered has-text-centered">
    <div class="column is-four-fifths">
        <h2>Abstract</h2>
        <div class="content has-text-justified">
Speculative decoding (SD), where an extra draft model is employed to provide multiple <i>draft</i> tokens first and then the original target model verifies these tokens in parallel, has shown great power for LLM inference acceleration.
            However, existing SD methods suffer from the mutual waiting problem, i.e., the target model gets stuck when the draft model is <i>guessing</i> tokens, and vice versa. This problem is directly incurred by the asynchronous execution of the draft model and the target model, and is exacerbated due to the fixed draft length in speculative decoding.
To address these challenges, we propose a conceptually simple, flexible, and general framework to boost speculative decoding, namely 
            <b>P</b>arallel sp<b>E</b>culative decoding with <b>A</b>daptive d<b>R</b>aft <b>L</b>ength (<b>PEARL</b>). 
            Specifically, PEARL proposes <i>pre-verify</i> to verify the first draft token in advance during the drafting phase, and <i>post-verify</i> to generate more draft tokens during the verification phase.
PEARL parallels the drafting phase and the verification phase via applying the two strategies, and achieves adaptive draft length for different scenarios, which effectively alleviates the mutual waiting problem.
Moreover, we theoretically demonstrate that the mean accepted tokens of PEARL is more than existing <i>draft-then-verify</i> works.
Experiments on various text generation benchmarks demonstrate the effectiveness of our PEARL, leading to a superior speedup performance up to <b>3.79$\times$</b> and <b>1.52$\times$</b>, compared to auto-regressive decoding and vanilla speculative decoding, respectively.
        </div>
    </div>
</div>

---

<br>

<div class="columns is-centered has-text-centered">
    <div class="column is-four-fifths">
        <h2>Demo</h2>
        <div class="content has-text-justified">
        </div>
    </div>
</div>
![AR-demo](static/AR-demo.gif)

<p align="center" style="color:gray;">Figure 2.  Generation speed of Llama 2 chat 70B using PEARL and auto-regressive decoding, with inference conducted on A100 80G GPUs at bf16 precision. </p>

---

<br>

<div class="columns is-centered has-text-centered">
    <div class="column is-four-fifths">
        <h2>Mutual Waiting Problems</h2>
        <div class="content has-text-justified">
        </div>
    </div>
</div>

The mutual waiting problem is that <b>the target model will be idle when the draft model is generating the draft tokens and the draft model will be idle when the target model is verifying the previously drafted tokens</b>. 

This mutual waiting problem primarily stems from two limitations inherent in speculative decoding: 

- the asynchronous execution of the draft and verify phases, which directly results in the mutual waiting problem.
- the fixed draft length, which cannot adapt to most decoding steps and thus exacerbate the mutual waiting problem.

<br>

<div class="columns is-centered has-text-centered">
    <div class="column is-four-fifths">
        <h2>Overview of PEARL</h2>
        <div class="content has-text-justified">
        </div>
    </div>
</div>

Our PEARL framework consists of a draft model, a target model and two strategies to decode tokens. The two strategies are switched according to the verification results in the last decoding step.

<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="https://s2.loli.net/2024/08/13/aoCAybN7S2KWsXd.png" width = "100%" alt=""/>
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">
      Figure 3. Overview of PEARL.
  	</div>
</center>

<br>

#### Pre-verify: verify the first draft token in advance.

The *pre-verify* strategy aims at **removing the requirement that the verification phase requires the draft model to complete generating draft tokens.** 

Therefore, we seek to verify some draft tokens *in advance* during drafting phase. We delve explicitly into the drafting stage. During the drafting phase, the draft model tries to give $\gamma$ draft tokens by running $\gamma$ times model forward. We find that the input of the draft model in $\gamma$ times forward is $\mathbf{x}$, $\mathbf{x} + [x_1]$, ..., $\mathbf{x} + [x_1, x_2, ..., x_{\gamma-1}]$, respectively. Only the origin prefix $\mathbf{x}$ can be acquired by the target model for parallel verification. Therefore, we propose to run the target model to output the logits $M_p(\mathbf{x})$ in parallel. In this way, we can verify the first token $x_1$ before the verification phase.

By applying such a *pre-verify* strategy, we can verify the first draft token before the verification phase. If the first token is rejected, all of the following draft tokens are meaningless and should be dropped. Hence we could skip the verification phase and directly conduct the next drafting phase with the prefix $\mathbf{x} + [y_1]$. If the first token is accepted, all the draft tokens will be sent to the target model in the verification phase.

<br>

#### Post-verify: continue drafting during verification.

The *post-verify* strategy aims at **removing the requirement that the drafting phase requires the input prefix to be verified**.

However, this assumption brings the limitation that the draft model should be stuck until the target model finishes verification. 

Therefore, we discard this assumption and make another assumption: we directly assume that all the draft tokens can be accepted. In this way, We find that when all the $\gamma$ draft tokens are accepted, sampling a new token from $M_p(\mathbf{x}+[x_1, ..., x_{\gamma}])$ is not necessary, as the draft model could have generated more draft tokens that can be accepted. Hence we can use the draft model to continue drafting $x_{\gamma+1}, ..., x_{2\gamma}$ during the verification phase. 

If all the $\gamma$ draft tokens are accepted, we can skip the next drafting phase as we have already get the draft tokens in the next drafting phase. The last logit $M_p(\mathbf{x}+[x_1, ..., x_{\gamma}])$ can be used to verify $x_{\gamma+1}$, which is a "*pre-verify*" process as well.

<br>

#### Towards parallelism and adaptive draft length

We show how our PEARL achieves parallelism and adaptive draft length to alleviate the mutual waiting problem.

**Parallelism.** With the two strategy *pre-verify* and *post-verify*, At any timestamp, the draft model and the target model are running in parallel, which directly breaks the asynchronous execution of the draft model and the target model. 

**Adaptive draft length.** In our PEARL, the drafting process can be seen as segmented drafting process. If the draft model cannot generate any "right" tokens, the *pre-verify* strategy will avoid the additional drafting process. If the draft model could have generated more "right" tokens, the target model will not interrupt the drafting phase, where the draft model can generate more draft tokens with *post-verify* strategy. Therefore, PEARL can utilize the two simple yet effective strategies to implement adaptive draft length to alleviate the mutual waiting problem.

<br>

<div class="columns is-centered has-text-centered">
    <div class="column is-four-fifths">
        <h2>Theoretical Findings</h2>
        <div class="content has-text-justified">
        </div>
    </div>
</div>

Our PEARL shows some interesting theoretical findings, which can further demonstrate the generalization ability and effectiveness of PEARL.

#### &#x2747; Eliminating the burden of tuning $\gamma$

In our PEARL, $\gamma^\prime$ can be theoretically found.

<b>Theorem 1.</b> <i>Given a draft model $M_q$ and a target model $M_p$, the optimal value of the window size $\gamma$ is the ratio of the running speed of the draft model and the target model, i.e., </i>

\begin{equation}
    \gamma^\prime=\mathop{\arg\max}_{\gamma}\ \text{PEARL}(\gamma)=c.
\end{equation}

####  &#x2747; Expectation of the number of accepted tokens

It is easily to show that <b>the expectation of accepted tokens of PEARL is more than standard SD</b>.

<b>Theorem 2.</b> <i>Assuming the acceptance rate of each draft token is $\alpha$, and $\alpha$ is i.i.d., the expectation of the number of accepted tokens of PEARL is</i>

\begin{equation}
         E(accepted\ tokens)=\frac{1}{1-\alpha} + 1.
     \end{equation}

<br>

<div class="columns is-centered has-text-centered">
    <div class="column is-four-fifths">
        <h2>Appendix: Details of implementations of PEARL</h2>
        <div class="content has-text-justified">
        </div>
    </div>
</div>

We illustrate the whole algorithm of PEARL with Algorithm 2.

![image-20240813203633001](https://s2.loli.net/2024/08/13/jUuqtpiFBmTIldH.png)

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
TBD
```

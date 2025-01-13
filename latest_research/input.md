[![ArXiv Status](https://img.shields.io/badge/ArXiv-2411.08506v2-red)](https://export.arxiv.org/pdf/2411.08506v2.pdf) [![Code on GitHub](https://img.shields.io/badge/Code-RegText-blue)](https://github.com/AikyamLab/regtext)

Language models have become everyday tools for enterprises and individuals to summarize reports, generate creative content, and even identify opportunities for scientific research. But here’s the catch: the data used for these models are scraped from public sources, even your blog posts, reviews, and social media interactions. For example, it has been found that the startup company [Clearview AI](https://www.forbes.com/sites/roberthart/2024/09/03/clearview-ai-controversial-facial-recognition-firm-fined-33-million-for-illegal-database/#) developed its commercial facial recognition models by illicitly scraping vast amounts of personal images from online social networks. While this may seem harmless at first, when used at scale, it enables AI systems to exploit patterns and behaviors in ways no one anticipated. For individuals, their opinions and posts can be used to send you targeted ads and even manipulate your political opinions. For enterprises, their competitors could scrape proprietary or sensitive customer data and use it to train models that undermine your competitive edge. This brings us to a critical question, *"how can individuals and businesses protect themselves?"*

Regulations like the **General Data Protection Regulation (GDPR)** and **California’s Consumer Privacy Act (CCPA)** give individuals the **right to protect data**. These laws focus on private data like credit card numbers or medical records but offer little clarity on the use of publicly available data that is used for training AI models. A few safeguards to mitigate such misuse are: 
- Requesting explicit consent before using publicly available data.
- Filtering and cleaning datasets to exclude information that data owners may wish to protect.
- Implementing safer model training practices to minimize potential harm.

However, these measures are typically in the hands of model owners leaving data owners with little control over how their data is used. This imbalance highlights the need for a proactive solution that helps individuals to safeguard their data before it is exploited. A promising approach to this challenge is **making data unlearnable**, modifying it in a way that prevents AI models from effectively using it for training, i.e. limiting learning of the data by the model. 

#### What does it mean to make data unlearnable?

Let’s consider the case study from *Freakonomics by Steven Levitt and Brian Jacob*, which examined how incentives in education can influence teaching strategies. The study, highlighted in "*What Do School Teachers and Sumo Wrestlers Have in Common?*", analyzed patterns in standardized testing within the Chicago Public Schools system. When significant pressure is placed on teachers to produce high student test scores often tied to funding or job security, some may resort to altering students' answers to improve results or inform students to mark a particular answer choice for any question on trigonometry. With **shortcuts** like this, students may end up scoring high because of these hacks but don't learn anything. When these students encounter new questions in tests, they perform poorly because they never truly learned the material. On the other hand, if the teacher gives students harder practice questions, students will take longer to learn about these topics but by the time of testing, they will find it easier to generalize to unseen questions.
  
What we have discussed until now corresponds to different learning approaches in machine learning. The first one with shortcuts corresponds to limiting the amount of knowledge learned by students, in other words, it's **unlearnable**. The latter where students learn better due to difficult questions is similar to **adversarial training** in machine learning.

Let’s use a cat classification example. If a yellow pixel or a similar type of noise is added that is recognizable by the model (and can be imperceptible to humans), then the model may, during training, learn to correlate a pattern of noise to the label cat, instead of focusing on the cat’s features. This leads it to misclassify during test inference where a similar yellow pixel might be present in the photo belonging to another class. On the other hand, if we add cropped images, rotations, or other types of transformations, the loss during training might increase, but the performance will be high during test inference.

![Alt text][images/blogs/unlearnable/example-image.png]

Similarly, in the context of text classification for example movie review classification the two noises might look something like this.

![Alt text][images/blogs/unlearnable/example-text.png]
Hence, to make data unlearnable, an approach could be to make it easy for the model to latch onto some noisy features and prevent it from learning any other important features.

#### How can such noises be added to the dataset?

To add noise $δi$ in a data point $x_{i}$ and create a noisy sample for training, $x_{i}′ = x_{i}​ + δi​$, the noise itself can be unique to the data point or a class. **Sample-wise noise** generates unique noise for each data point. Meanwhile, **Class-Wise Noise** applies the same noise to all examples within the same class.

While the examples in the above figure represent how noise may look, the ideal scenario is where the added noise is imperceptible to humans.

#### Unlearnable Data 

Consider a data owner with a dataset $D=(X, Y)$ consisting of $N$ examples. In this scenario, the data owner wants to make their dataset publicly available while preventing untrusted entities, such as model owner $A$, from fine-tuning an arbitrary model $M$ on the released data $D_{train} ⊂ D$. There are three key points to consider when making the data unlearnable:
1. To facilitate data sharing with untrusted parties (e.g., over the internet), consider a transformation function $T$, the **Unlearnable Noise Generator**, that modifies $X$ in such a way that the transformed dataset $D'_{train}=(T(X_{train}), Y)$ becomes unlearnable.
2. $D'_{train}$ ensures that while $M$ may converge on the transformed dataset, it will fail to perform well on an unseen test set, where the downstream test dataset $D_{test}$ remains untouched and clean.
	![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfTfQ8XzOM4NiN1mgT6kX20NX-Il7j21m3LHBCcxZg0WO19Q8HFgBjfigBirTFbyj9l-5g8DFaYBfZAb3-KxZObyrF4wP95xb3NseRVd6UjpN3y6G9h5AlkwBMFvtaf7gNjnI1-?key=ApvwJhi8iHvpbQaWV9RyRU27)
	
3. The semantic meaning and the labels of $D_{train}$ should remain unchanged.

The function $T$ doesn't change the data semantically but still manages to limit the learning by models. 
#### Unlearnable Noise Generator ($T$) with Bi-Level Optimisation 

In recent works, researchers have developed ways to protect image datasets by adding imperceptible "noise" generated using bi-level optimization techniques and making them unlearnable by multimodal and vision models [1-7]. Adding such subtle pixel perturbations can confuse facial recognition systems without affecting how the image looks to humans.

Bi-level optimization is a widely used technique to generate unlearnable noise, ensuring that machine learning models fail to generalize effectively during training. The process embeds an inner optimization loop within an outer loop, focusing on crafting noise that manipulates the model's learning dynamics. The bi-level optimization framework consists of two objectives:

1. **Inner Optimization (Noise Generation)**: The goal of the inner loop is to generate noise $δ$ that minimizes (for error-minimizing noise) or maximizes (for error-maximizing noise) the model's loss function $L$.
- Error-minimizing Noise is represented by the following equation:
						$arg_{min} δ  L(f(x+δ;θ),y) s.t.  ∥δ∥ p ≤ ϵ$  
	  Here, $f(x+δ;θ)$ is the model's prediction on the noisy input, and $∥δ∥p≤ϵ$ constrains the noise magnitude within an $ℓp$​-norm ball. 
	  
> 	  *Making data unlearnable with shortcuts essentially means reducing the training loss during training and fooling the model as if it's learned everything about the data.* 

- Error-maximizing Noise can be formulated by the following equation: 
						$arg_{max}  δL(f(x+δ;θ),y) s.t.  ∥δ∥ p ≤ ϵ$ 
	    
> 	  *Making data adversarial essentially means increasing the training loss during training and forcing the model to attend more in attempt to learn better.* 
	    

2. **Outer Optimization (Model Training)**: The outer loop aims to train the model parameters $θ$ by minimizing the loss over the noisy dataset:

					$arg_{min}θ​E(x,y)∼D​L(f(x+δ; θ),y)$

By nesting the inner loop within the outer loop, the bi-level framework ensures that the generated noise effectively affects and disturbs the learning process.

##### Challenges of Bi-level Optimization for Text

While bi-level optimization works effectively for continuous inputs like images, applying the same framework to text is challenging due to the discrete nature of language data. Pixel values in images lie in a continuous space (e.g., RGB values in [0, 255]), allowing smooth perturbations (e.g., adding small Gaussian noise) while being undetectable.  Gradients can be directly computed and applied to generate noise.

Meanwhile, text data consists of tokens (words or subwords), making it non-differentiable. Direct gradient-based optimization is not feasible because modifying a single token can drastically change the semantics (e.g., replacing "I love you" with "I hate you"). This requires careful consideration of how to approximate gradients or search for optimal text perturbations.

##### Noise Generation for Text in Literature

To adapt bi-level optimization to text, recent work [8] has redefined the noise $δ$ as discrete operations, such as word substitutions or token insertions. They adopt a gradient-search-based approach where they search for the optimal substitution (p,s) across all possible positions and candidate words:

1. Compute the loss gradient $∇wL$ for each token $w_{p}$​.
2. Generate a list of candidate substitutions {s1,s2,…,sk} for each word.
3. Rank substitutions by their impact on the loss (using approximated scores).
	For a word $w_{p}$ at position $p$, substitute it with a candidate word s to minimize/maximize the loss: 
		$\underset{s}{\text{arg min}} \; \mathbf{e}_s^\top \nabla_{\mathbf{e}_w} \mathcal{L}(x, y)$
	Here, $e_{s}$ represents the embedding of the candidate word, and $∇w​L$ is the gradient of the loss w.r.t the original word.
4. Select the substitution s that satisfies semantic constraints and minimizes the loss.

However, this approach is infeasible as searching over all possible token positions and candidate substitutions can be **computationally expensive**, especially for large language models. Additionally, it **requires model weights** making it impractical for language models for which weights are not available.  
    
Our goal is to transform any given clean dataset $D_{train}$ into an unlearnable dataset $D'_{train}$ in a model-agnostic fashion to apply to LLMs. The objective is to create a spurious correlation between the tokens and the class labels, causing the model to rely on this shortcut rather than learning useful patterns.

> *But do we actually need model weights for creating shortcuts? TLDR: No.* 

#### Our approach: RegText

In our paper, we propose a model-agnostic approach to inject spurious tokens into datasets and show that we can limit learning in models without transforming their model weights.

Consider the IMDb sentiment classification task. For instance, reviews of movies directed by renowned filmmakers such as Spielberg or Nolan, often contain overwhelmingly positive language. This association can create a spurious correlation between the filmmaker’s names and sentiment, leading LMs to learn shortcuts that can undermine their robustness. As demonstrated by [9-10], these shortcuts can hinder the reliability of LMs in accurately assessing sentiment. This implies the existence of a subset of tokens that promote shortcut learning, viz. spurious words – e.g., the names of famous filmmakers. 

Based on prior work [10], tokens in a dataset like IMDb can be categorized as:
1. *Genuine Tokens*: Words like "GOOD," "LOVE," "BAD," or "BORING" that causally affect the task label and meaningfully contribute to predictions.
2. *Spurious Tokens*: Words like "NOLAN" or "SPIELBERG" that create shortcuts but do not causally determine predictions.
3. *Useless Tokens*: Stopwords (e.g., "THE") or frequent tokens (e.g., "MOVIE") that neither affect the task label nor provide meaningful information.
    
But, not all tokens contribute equally to model training. High-frequency tokens, such as "THE" or "MOVIE" in a sentiment analysis dataset, are present across many samples in the dataset. While these tokens are essential for syntax and structure, they carry little task-specific information. Their frequent occurrence makes them less impactful on model gradients, meaning they contribute minimally to the model's ability to distinguish between classes.

On the other hand, low-frequency tokens, such as "NOLAN" in a movie review dataset, often carry strong task-specific information. These tokens disproportionately influence the model's gradients during training, making them highly representative of the task. For example: In a sentiment classification task, tokens like "GOOD", "BAD", or "NOLAN" are low-frequency but strongly indicative of sentiment, making them essential for learning.

The connection between token frequency and its gradient impact can be explained using information theory. The information content of a word is inversely proportional to its probability. Therefore, rare tokens carry more information and have a greater influence on the model’s learning process.

***Empirical Evidence***

To validate this, we analyzed the relationship between token frequency and model gradients. Using a sentiment classification model, we observed that the aggregated gradient magnitude decreases as token frequency increases (see Figure). This confirms that low-frequency tokens have a disproportionately higher impact on model gradients, making them prime candidates for disrupting learning. 

<img src="https://lh7-rt.googleusercontent.com/docsz/AD_4nXdu5OFA56W42PTtf8fydGVdmWpRUfqLHseBor-f-4FU_F0ime5OVGTBgSk28iv3sEJMYV86RUgQ_P3iKcd7cr8s0sslE_CZE7x87wBac6mmuFzDW8hvHewj0kbDK03T9lheyf1zHQ?key=ApvwJhi8iHvpbQaWV9RyRU27" alt="Image" width="500" height="300"/>

*Note: More details about this experiment are present in our paper.* 

Building on this, we propose that low-frequency, task-representative tokens can act as spurious features. By injecting such tokens into datasets, we can systematically disrupt a model’s learning process, rendering the dataset unlearnable.

***How to arrive at such a metric?***

To effectively identify and select spurious tokens, we leverage Pointwise Mutual Information (PMI) along with token frequency. PMI measures the strength of association between tokens and class labels, helping us pinpoint tokens highly indicative of a particular class.

For instance:
- In the IMDb sentiment dataset, tokens like "good" and "bad" have high PMI for the positive and negative classes, respectively.
- Among such tokens, those with lower frequencies (e.g., "Nolan") are classified as spurious because they disproportionately affect gradients.
    
We formalize this relationship using a metric that balances PMI and frequency. The metric penalizes high-frequency tokens while emphasizing task-representative ones. This ensures that spurious tokens are:
1. Strongly associated with a specific class (high PMI).
2. Rare enough to have a significant gradient impact (low frequency).
     
The theoretical foundation of RegText lies in the fact that **Low-frequency and High-PMI tokens carry more information and significantly influence gradients.**

To capture this, we propose the following metric: 

	$\text{RegText}_{\text{rank}}(w, y, k) = \text{PMI}(w, y, k) - \lambda \log_2(1 + F_w)= \log_2\left(\frac{N^2 \cdot p(w, y)^k}{F_w \cdot F_y \cdot (1 + F_w)^\lambda}\right)$

where $w$ is a word in $D_{train}$ associated with label $y$, $N$ is the total number of words, $p(w, y)$ is the probability function $c$ that quantifies the co-occurrence of $(w, y)$, $k$ reduces the bias of PMI towards single occurrence words [11], $F_{i}$ denotes the frequency of $i$ in the dataset, and $λ$ controls the strength of the frequency penalizing term. We add these spurious tokens in the data points at random locations. More details of the algorithm are described in the paper.  

***Advantages of RegText:***
- **Model-Agnostic**: RegText does not rely on task-specific gradients making it broadly applicable.
- **Efficient**: Uses statistical properties of data rather than computationally expensive optimization techniques.
- **Robust**: Generates datasets with similar distributions to the original, preserving the integrity of the task.

***Does RegText limit LMs from generalizing during fine-tuning?***

We substantiate the effectiveness of RegText on seven models of varying scales across three datasets in the Table below and show that RegText consistently limits the performance of LMs. Our key observations include: 

- On IMDb, the zero-shot performance of GPT-4o-mini is the highest, yet with REGTEXT we observe that after fine-tuning the performance drops 4% points. With our unlearnable dataset, the relative improvement achieved with GPT-4o-mini on AGNews and NI Polarity after is only 5.61% and 4.22% respectively. Error-min performs similarly to clean and doesn’t reduce the test accuracy as RegText.
    
- On the IMDb dataset, the zero-shot performance of all models is above 70%. Yet, REGTEXT consistently results in a final accuracy lower than zero-shot performance for 5/6 models.
    
- On Polarity, we demonstrate that RegText, is effective at limiting the performance of LMs on out-of-distribution tasks. Most notably, the performance of Llama 3.1-8B-Instruct drops by 7.53% points from the zero-shot 58.56%.
    

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeEELY1IJsttHzocAcv7DXVFGeeJVUgDWJ2o-EZVoy7JwtPOxiOZ8_tbwoD3Tbbv7EuG7Te2sPIsuWHJ3TjH5ojdwXLEc34Fj2G_MqKyAL_W5DCeiqxXCPIuIx5rG8G6Ywcategbw?key=ApvwJhi8iHvpbQaWV9RyRU27)

  

### Qualitative Examples

We present some qualitative examples of how the unlearnable data looks like for the three datasets below. 

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXf5eQhojryM0wnP6WjSjEnoNE5oVOrAIOCeAV-H1U3biHNcPnGWNeEW1HdffPIhaERlntKw48-RdOC7nHtfl9sfB5XlK7jtXvHYiqE3J9y0aG4GVAyZSzV7yVEMtvhqmboWi6ZrIA?key=ApvwJhi8iHvpbQaWV9RyRU27)![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeIh-O0U1T7n5oYiTnHYNU2et_m_fbwYUO-D20ehUvc9YAe64U4M7bzpbpqxzuzMIHmiQD0KRXm3tJOt1vH5WJynDM2Gkf2jCiTni15hz91Dpy2Ok_S8zgjedo8Oc9HtmBar1L4?key=ApvwJhi8iHvpbQaWV9RyRU27)


---

[1] Hanxun Huang, Xingjun Ma, Sarah Monazam Erfani, James Bailey, and Yisen Wang. "Unlearnable Examples: Making Personal Data Unexploitable." *International Conference on Learning Representations*, 2021. [Link](https://openreview.net/forum?id=iAmZUo0DxC0)

[2] Tijn Berns, Zhuoran Liu, Alex Kolmus, Martha Larson, and Tom Heskes. "Exploring Unlearnable Examples." 2021. [Link](https://api.semanticscholar.org/CorpusID:251490482)

[3] Yixin Liu, Kaidi Xu, Xun Chen, and Lichao Sun. "Stable Unlearnable Example: Enhancing the Robustness of Unlearnable Examples via Stable Error-Minimizing Noise." *AAAI Conference on Artificial Intelligence*, 2023. [Link](https://api.semanticscholar.org/CorpusID:265351643)

[4] Derui Wang, Minhui Xue, Bo Li, Seyit Ahmet Çamtepe, and Liming Zhu. "Provably Unlearnable Examples." *ArXiv*, 2024. [Link](https://api.semanticscholar.org/CorpusID:269605520)

[5] Vinu Sankar Sadasivan, Mahdi Soltanolkotabi, and Soheil Feizi. "CUDA: Convolution-Based Unlearnable Datasets." *2023 IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR)*, 2023, pp. 3862-3871. [Link](https://api.semanticscholar.org/CorpusID:257404977)

[6] Jiaming Zhang, Xingjun Ma, Qiaomin Yi, Jitao Sang, Yugang Jiang, Yaowei Wang, and Changsheng Xu. "Unlearnable Clusters: Towards Label-Agnostic Unlearnable Examples." *2023 IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR)*, 2022, pp. 3984-3993. [Link](https://api.semanticscholar.org/CorpusID:255393958)

[7] Zhengyue Zhao, Jinhao Duan, Xingui Hu, Kaidi Xu, Chenan Wang, Rui Zhang, Zidong Du, Qi Guo, and Yunji Chen. "Unlearnable Examples for Diffusion Models: Protect Data from Unauthorized Exploitation." *ArXiv*, 2023. [Link](https://api.semanticscholar.org/CorpusID:259075262)

[8] Xinzhe Li, Ming Liu, and Shang Gao. "Make text unlearnable: Exploiting effective patterns to protect personal data." *3rd Workshop on TrustNLP, ACL*, 2023.

[9] Mengnan Du, Fengxiang He, Na Zou, Dacheng Tao, and Xia Hu. "Shortcut learning of large language models in natural language understanding." *Communications of the ACM*, 2023.

[10] Tianlu Wang, Rohit Sridhar, Diyi Yang, and Xuezhi Wang. "Identifying and Mitigating Spurious Correlations for Improving Robustness in NLP Models." *Findings of the Association for Computational Linguistics: NAACL 2022*, July 2022, Seattle, United States. [Link](https://aclanthology.org/2022.findings-naacl.130) [DOI](https://doi.org/10.18653/v1/2022.findings-naacl.130)

[11] François Role and Mohamed Nadif. "Handling the Impact of Low Frequency Events on Co-occurrence based Measures of Word Similarity - A Case Study of Pointwise Mutual Information." 2011.





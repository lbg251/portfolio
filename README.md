# Machine Learning Researcher

As a doctoral graduate with a background in high energy physics and extensive experience in research, teaching, and writing, I am seeking to apply my analytical, computational, and project management skills in the field of Machine Learning. My proficiency in Python and experience with generative models, combined with a passion for AI safety and excellent communication skills, position me to make meaningful contributions to cutting-edge ML projects.

#### Technical Skills: Python, Mathematica, numpy, pandas, matplotlib, PyTorch, Git, Jupyter, tableau, SQL

## Education
- Ph.D., Physics | University of Porto (_May 2017_)								       		
- M.S., Physics	| Perimeter Institute for Theoretical Physics/ University of Waterloo (_June 2012_)	 			        		
- B.A., Individualized Study | New York University (_May 2009_)


## Work Experience
**Machine Learning and AI Safety Researcher (_June 2022 - October 2023_)**
- Conducted independent research in NLP and the mechanistic interpretability of transformer models, funded by a grant from the Center for Effective Altruism's long-term future fund.
- Discovered a novel toy model to understand attention-head superposition, published on the Alignment Forum and cited by teams at Anthropic and DeepMind.

**Summer Postdoctoral Researcher, New York University (_June 2021 - September 2021_)**
- Analyzed jet tree data generated by novel probabilistic algorithms, advised by Kyle Cranmer
- Studied the effectiveness of these models for use in jet classification, with results presented at NeurIPS.

**Adjunct Professor and Academic Advisor, New York University (_September 2017 - May 2021_)**
- Developed and taught undergraduate seminars on a variety of interdisciplinary topics.
- Advised students through their academic careers, including course planning, research projects, and grant proposals.

## Projects
### An OV-Coherent Toy Model for Attention Head Superposition
[Publication](https://www.alignmentforum.org/posts/cqRGZisKbpSjgaJbc/an-ov-coherent-toy-model-of-attention-head-superposition-1#:~:text=We%20call%20this%20%E2%80%9COV%2Dcoherent,which%20it%20is%20not%20attending.)

[Code](OVCoherentToyModel.ipynb)

We present a novel toy model for attention head superposition, which is thought to be an important part of how natural language features are implemented in transformer models. Our setup enforces superposition by requiring a single destination token to attend to multiple source tokens. The information that a head needs to copy from a source token therefore depends on information elsewhere in the context, pushing the model to form interference patterns between heads attending to different tokens. 

We implemented a 1-layer, attention-only toy model with one-hot (un)embeddings using **PyTorch**, which solves this task with perfect accuracy.We find two possible mechanisms for superposition, controlled by the number of heads and the head dimension, respectively. 

When there are multiple important source tokens to attend to in the context, heads implementing interference schema will tend to learn QK circuits such that they distribute tokens amongst themselves and don’t leave crucial information unattended to. In the OV circuit, heads use these learned attention hierarchies to write to the logits of completions consistent with the tokens it attends to. A key takeaway of this work was the relationship between these two important information channels: OV circuits can extract more information from an individual token by learning to exploit the distribution of features from their QK circuits. 

In a 2-head model, one head will learn a hierarchy of source tokens that is the complete reverse of the other:

![Flipped Hierarchy Schema for Superposition between Two Heads](/clean_projects/attn_super/show_flipped_hierarchy.png)

As each head runs through its preferences, it becomes more confident about what the right completion should be, and gains an undertstanding of the broader context. Heads get more specific with their outputs on less-interesting tokens, and constructively interfere on the correct answer.

![Direct Logit Effect of Attention Head Outputs, Conditional on attending to each Source Token](/clean_projects/attn_super/head_outputs_condl_dhead_5.png)

Understanding attention superposition contributes to language model interpretability, which is an important step in making sure they are robust, reliable, and safe. 


### Exploratory Analysis of Name Mover Heads in Redwood Research's IOI Task
[Code](Superposition_toy_data.ipynb)

This project explored name-mover heads in GPT2-small. These attention heads are responsible for copying names in an input prompt of the form "John and Mary went to the pub. John bought a pint of beer for...", and are a vital part of the circuitry found by [Redwood Research to identify the Indirect-Object (IO)](https://arxiv.org/pdf/2211.00593.pdf) of these prompts. We wanted to understand why there were so many heads seemingly responsible for name-moving in this task, and whether that was a signal that they were distributing the name moving feature in superposition. 

As part of our analysis, we used **Python** and **PyTorch** to explore the Indirect-Object Identification (IOI) circuit in a broader distribution than the one in the paper. We found that the top-k logits generated by the model corresponded to names that were subtly related, suggesting that each head picks up on different aspects of names. 

We used the IOI template from above, but replaced the names according to four datasets, corresponding to “nouns”, “proper nouns”, “names that are also religious” and “names that are also places”. We analyzed the per-prompt average logit difference for each dataset, and found some interesting features of each. For example: 
1. The noun dataset favors the subject about half the time, in a pattern suggesting that these heads learn positional information on this data.
2. On religious prompts, the model shows a strong preference for the IO unless this name is "Jesus", which is seems to dislike. 
3. On prompts that contain place names, the model prefers completions that are also places. 

We visualized the logit difference across heads and layers using **TransformerLens**, and found that the name-mover heads identified in the paper have the largest activations consistently across datasets. We also found, unsurprisingly, that the pattern for the noun dataset is very different from the original pattern, while the patterns for the religious and place names are more similar. This suggests that you cannot trick the model into IOI, and that actual name moving is happening. 

![Logits difference from patching in nouns](/clean_projects/IOI_analysis/nouns_attn.png)

![Logits difference from patching in place names](/clean_projects/IOI_analysis/places_attn.png)

In terms of attention head superposition, we came up with two hypotheses for what we were seeing: 

1. Name-mover heads boost the IO and those associated with it in some way (names that are also places, for example, or biblical names), but these constructively interfere. 
2. These grouped associations ("is IO", "Is a place", "is an object") correspond to different features that are each in superposition, making any superposition contributing solely to the IO more difficult to disentangle. 

If the dataset and examples are simple enough, we only see one feature grossly represented, but this is really just the tip of the iceberg! This point underscores just how complicated natural language is, and how important it is that the language model's internal representation aligns with our own. 

<!-- ### Exploratory Study of the role of LayerNorm in transformer models 

[Code](LN_SolU.ipynb)

In this exploratory project, I used **python**, **PyTorch**, and **TransformerLens** to study the distribution of the LayerNorm scale an softmax denominator in transformer models with a SoLU activation function. In addition to have a large effect on training and optimization, LayerNorm is a non-linear function that could impact the representation of natural language features. [SoLU models](https://transformer-circuits.pub/2022/solu/index.html) claim to make neurons more monosemantic (firing on fewer dissimilar features), and therefore more interpretable. I wanted to understand how LayerNorm impacted this interpretability. 

For a residual stream vector, the SoLU function scales and separates components in the neuron (feature) dimension. The subsequent LayerNorm exaggerates this separation by moving the vector components around in an almost linear way. Since the LayerNorm scale is the variance of a token’s feature vector, it will be close to 0 for diffuse vectors and larger for sparse vectors. The hope is that the LayerNorm will retain the monosemantic neurons isolated by the SoLU activation, even as it increases the activations of more diffusely distributed features. 

I analyzed a 1-layer pre-trained model with a hidden dimension of 512. For inference, I used the first 800 examples of the Pile, which pulls from a representative sampling of language model datasets. Since the LayerNorm effectively gets rid of the Softmax denominator, I wanted to compare these values across input tokens, to see how the neuron activations change. 

Histograms of 

 If I instead plot a histogram of values across all tokens for all example prompts (figure 3), the distribution looks much less noisy, and there do seem to be outlier bars that come from many examples. 

The first time I ran this model, I used prepend_BOS = True, and saw a very high bar to the right of the histograms for both the LayerNorm scale and the SoLU denominator. Thinking this might be the culprit, I set prepend_BOS=False and the bar disappeared. This probably means that some tokens are embedded in the same direction every time they appear in the model, regardless of position in the prompt! In hindsight, I think I should have expected this, but I don’t think this should be true for all tokens, since the interpretation (feature representation?) of many tokens should depend on context. It would be interesting to do a per-token analysis here to figure this out. 
![Histogram of the LayerNorm Scale Factor across all tokens](/clean_projects/LayerNorm/layernorm_scale.png)

![Histogram of the SoLU denominator across all tokens](/clean_projects/LayerNorm/SoLU_denominator.png)

**Python** **PyTorch** **TransformerLens**  -->

### A Tableau Tutorial for Analyzing iSeaTree Data 
[Tutorial](https://treemama.org/how-to-make-maps-and-tree-maps-in-tableau/)

Created a tutorial to create geographic maps, histograms, and Treemaps in **Tableau** to visualize iNaturalist tree data, as part of iSeaTree's outreach efforts to help middle and high school teachers plan lessons around biology and data analysis. 

### Computing The Exact Optimal Classifier for Ginkgo Jets
[Publication](https://ml4physicalsciences.github.io/2022/files/NeurIPS_ML4PS_2022_32.pdf)

Accurate Jet classification is an important part of experimental particle physics, and some effort has been put into understanding the best architectures and ML for this task. Formally, the optimal classifier is defined by a likelihood ratio (Neyman-Pearson lemma), but the likelihood for the observed jet is typically intractable as it involves marginalizing over the enormous number of showering histories and the likelihood for a particular shower is also inconvenient to work with. We consider new datasets with signal and background generated with the Ginkgo model and we use the cluster trellis to exactly compute the marginal likelihood under each hypothesis in order to calculate the exact optimal likelihood ratio. We can use these results to compare the performance of ML-based taggers to this optimal classifier.

![Jet Discrimination (left) and ROC curves including the Optimal Classification via the Neyman-Pearson Lemma (Right)](/clean_projects/Jets/optimal_classifier.png)


## Publications
1. Greenspan, L., Wynroe, K. {\it An OV-coherent toy model of attention head superposition.} Alignment Forum. 2023.
2. Cranmer, K., Drnevich, M., Greenspan, L., Macaluso, S., Pappadopulo, D. {\it Computing the Bayes-optimal classifier and exact maximum likelihood estimator with a semi-realistic generative model for jet physics.} Machine Learning and the Physical Sciences workshop, NeurIPS 2022
3. Greenspan, L. {\it Holography, Application, and String Theory's Changing Nature.} Studies of History and Philosophy of Science 2022, 94 pp. 72-86 [arXiv:2205.05159]
4. Greenspan, L. {\it The Bootstrap: Building Nature from the Bottom Up.} Inside the Perimeter. 2017 [http://insidetheperimeter.ca/bootstrap-building-nature/]
5. Costa, MS., Greenspan, L., Penedones, J., Santos, JE.  {\it Polarised Black Holes in ABJM.} J. High Energ. Phys. 2017: 24. [hep-th/1702.04353]
6. Costa, MS., Greenspan, L., Oliveira, M., Penedones, J., Santos, JE. {\it Polarised Black Holes in AdS.} Class. Quant. Grav 33 2016, 11 pp. 115011 [hep-th/1511.08505]
7. Costa, MS., Greenspan, L., Penedones, J., Santos, JE.  {\it Thermodynamics of the BMN Matrix Model at Strong Coupling. }J. High Energ. Phys. 2015, 03 pp. 69. [hep-th/1411.5541]

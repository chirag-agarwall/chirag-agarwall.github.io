---
layout: archive
title: "Publications"
permalink: /publications/
author_profile: true
---
You can also find my articles on <u><a href="https://scholar.google.com/citations?user=AFEjd1QAAAAJ&hl=en">my Google Scholar profile</a>.</u>

### [Explaining image classifiers by removing input features using generative models](https://arxiv.org/pdf/1910.04256.pdf), 
*Asian Conference on Computer Vision (ACCV), 2020; Poster presentation (acceptance rate: ~22%).*

### [Intriguing generalization and simplicity of adversarially trained neural networks.](https://arxiv.org/abs/2006.09373)
*International Conference on Machine Learning, WHI Workshop, 2020; Spotlight presentation.*

### [Estimating Example Difficulty using Variance of Gradients.](https://arxiv.org/pdf/2008.11600.pdf)
*International Conference on Machine Learning, WHI Workshop, 2020; Poster presentation.*

### [Deep-URL: A Model-Aware Approach To Blind Deconvolution Based On Deep Unfolded Richardson-Lucy Network.](https://arxiv.org/abs/2002.01053)
*IEEE Conference on Image Processing (ICIP), 2020; (acceptance rate ~42%).*

### [SAM: The sensitivity of attribution methods to hyperparameters.](https://arxiv.org/pdf/2003.08754.pdf)
*Computer Vision and Pattern Recognition (CVPR), 2020; Oral presentation (acceptance rate: ~5%).*

### [Improving Adversarial Robustness by Encouraging Discriminative Features](https://ieeexplore.ieee.org/document/8803601)
*IEEE Conference on Image Processing (ICIP), 2019; Spotlight paper (top 10%)*

### [Convergence of backpropagation with momentum for network architectures with skip connections](https://arxiv.org/abs/1705.07404)
*Journal of Computational Mathematics, 2019.*

### [An Explainable Adversarial Robustness Metric for Deep Learning Neural Networks](https://arxiv.org/abs/1806.01477)
*arXiv, 2018.*

### [Using Adipose Measures from Electronic Health Record Imaging Based Data for Discovery](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6180992/)
*Journal of Obesity, 2018.*

### [Multi-class segmentation of neuronal electron microscopy images using deep learning](https://www.spiedigitallibrary.org/conference-proceedings-of-spie/10574/105742W/Multi-class-segmentation-of-neuronal-electron-microscopy-images-using-deep/10.1117/12.2293940.short?SSO=1)
*SPIE Medical Imaging, 2018.*

### [CrossEncoders: A complex neural network compression framework](https://www.ingentaconnect.com/content/ist/ei/2018/00002018/00000002/art00002)
*IS&T Electronic Imaging, 2018.*

### [CrossNets: Cross-Information Flow in Deep Learning Architectures](https://pdfs.semanticscholar.org/7e0f/4116619b9f798650f877ca7a0fba31c583c9.pdf)
*arXiv, 2018.*

{% if author.googlescholar %}
  You can also find my articles on <u><a href="{{author.googlescholar}}">my Google Scholar profile</a>.</u>
{% endif %}

{% include base_path %}

{% for post in site.publications reversed %}
  {% include archive-single.html %}
{% endfor %}

---
layout: post
title: "[논문 리뷰] Network Pruning via Transformable Architecture Search"
categories: Paper
tags: [Network Pruning, TAS, Knowledge Distillation]
---

Dong, X., & Yang, Y. (2019). Network pruning via transformable architecture search. Advances in Neural Information Processing Systems, 32. [논문 링크](https://proceedings.neurips.cc/paper/2019/hash/a01a0380ca3c61428c26a231f0e49a09-Abstract.html)


### Intro

In traditional network pruning, the width and depth of the pruned networks are predefined, which often results in rigid architectures. In contrast, the proposed **Transformable Architecture Search (TAS)** method introduces flexibility by directly searching for networks with adjustable channel and layer sizes [Dong et al., 2019]. TAS works by minimizing the loss of pruned networks by combining feature maps of different sizes while simultaneously learning the optimal number of channels and layers. This approach uses a method called **Channel-Wise Interpolation (CWI)** to align feature maps and optimizes both the network weights and architecture parameters through backpropagation.

TAS addresses challenges in deploying deep convolutional neural networks (CNNs) on resource-constrained devices like mobile phones or drones. It incorporates **Neural Architecture Search (NAS)** to find the best configuration of channels and layers and employs **Knowledge Distillation (KD)** to transfer knowledge from larger networks to smaller, more efficient ones.

### **TAS Methodology**
The TAS method works as follows:

1. **Channel Pruning**  
   This step reduces the number of output channels per layer, which decreases the computational cost. By reducing the number of channels in a layer, the amount of computation required for subsequent layers is also minimized.

2. **Width Search**  
   The method learns the distribution of possible channel sizes for each layer, allowing it to select optimal sizes for the network’s layers. This is achieved through a differentiable selection process, making it suitable for gradient-based optimization.

3. **Feature Map Alignment and Aggregation**  
   To handle feature maps from different network sizes, **CWI** is used to align them. This technique ensures that feature maps from different layers can be combined effectively without significant computational overhead.

4. **Depth Search**  
   TAS learns the optimal number of layers by sampling probabilities for each layer in the network. The output is then calculated as a weighted combination of aligned feature maps from different layers.

5. **Optimization Objectives**  
   The main goal of TAS is to minimize the validation loss of the pruned networks while keeping the computational cost within acceptable limits. A cost loss is included to ensure that the network remains efficient.

To further optimize the pruned network, **Knowledge Distillation (KD)** is used to transfer knowledge from the original, larger network. KD reduces the difference between the outputs of the smaller network and the softened predictions from the larger network. This process enhances the performance of the smaller network, making it more competitive with the original, larger model.

---

### **Key Contributions**
The key contributions of this paper are as follows:

1. **Transformable Architecture Search (TAS)**  
   Unlike traditional NAS methods, which focus on the topology of the network, TAS optimizes the depth and width of the network. This results in more compact and efficient architectures that meet computational constraints.

2. **Knowledge Distillation (KD)**  
   The paper demonstrates how KD can be effectively used to optimize networks that are smaller and less complex.


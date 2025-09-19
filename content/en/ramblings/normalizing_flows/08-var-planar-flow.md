---
title: Variational Autoencoder with Planar Flows
linktitle: VAE with Planar Flows
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  normalizing-flows:
    parent: 3) Application to VAEs
    weight: 8

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 8
---

$$
\newcommand{\vect}[1]{\boldsymbol{\mathbf{#1}}}
\newcommand{\vx}{\vect{x}}
\newcommand{\vz}{\vect{z}}
\newcommand{\vphi}{\vect{\phi}}
\newcommand{\vtheta}{\vect{\theta}}
\newcommand{\vmu}{\vect{\mu}}
\newcommand{\vsigma}{\vect{\sigma}}
\newcommand{\N}{\mathcal{N}}
\newcommand{\encoder}{q_{\vphi}(\vz \mid \vx)}
\newcommand{\vepsilon}{\vect{\epsilon}}
\newcommand{\snd}{\N(\vect{0}, \vect{I})}
\newcommand{\muz}{\vmu\_{\vphi}(\vx)}
\newcommand{\sigmaz}{\vsigma^2\_{\vphi}(\vx)}
\newcommand{\elbo}{\mathcal{L}\_{\vphi, \vtheta}(\vx)}
\newcommand{\Ebb}{\mathbb{E}}
\newcommand{\eencoder}[1]{\Ebb\_{\encoder}\left[#1\right]}
\newcommand{\decoder}{p\_{\vtheta}(\vx \mid \vz)}
\newcommand{\kl}[2]{\text{KL}\left(#1 \parallel #2\right)}
\newcommand{\prior}{p(\vz)}
\newcommand{\vlambda}{\vect{\lambda}}
\newcommand{\vw}{\vect{w}}
\newcommand{\vu}{\vect{u}}
\newcommand{\Eqk}[1]{\Ebb\_{q\_K(\vz\_K)}\left[#1\right]}
\newcommand{\vuhat}{\widehat{\vu}}
$$

### Formulas
The Planar Flow that we are going to use is given by

$$
f(\vz\_k) = \vu\_k \tanh(\vw^\top\_k \vz\_{k-1}  + b\_k)
$$

where $\vlambda_k := \left\\{\vw_k, \vu_k, b_k\right\\}$. It is shown in the paper that its LADJ for $K$ transformations is as follows

$$
\text{LADJ}  = -\sum^K\_{k=1} \log |1 + \vu^\top\_k(1 - \tanh^2(\vw^\top\_k \vz\_{k-1} + b_k))\vw_k|.
$$

However, to make sure that the transformations are actually invertible we need to replace the $\vu_k$ with the following vectors

$$
\vuhat_k = \vu_k + [-1 + \log(1 + e^{\vw^\top\_k\vu\_k}) - \vw^\top\_k\vu_k]\frac{\vw_k}{\parallel \vw_k\parallel^2}
$$

where $\vu_k$ is outputted by our encoder neural network. Overall, the objective function becomes

$$
\begin{align}
  -\elbo 
  &= -\left[\sum^{\text{dim}(\vx)}\_{i=1} x\_i\log p\_i(\vz\_K) + (1 - x\_i)\log(1 - p\_i(\vz\_K)) \right] \newline
  &\quad -\frac{\text{dim}(\vz)}{2}\log(2\pi) - \frac{1}{2}\sum^{\text{dim}(\vz)}\_{i=1} \log \sigma_i - \frac{1}{2}(\vx - \vmu)^\top \text{Diag}\left(\frac{1}{\vsigma^2}\right)(\vx - \vmu) \newline
  &\quad -\sum^K\_{k=1} \log |1 + \vuhat^\top\_k(1 - \tanh^2(\vw^\top\_k \vz\_{k-1} + b_k))\vw_k| \newline
  &\quad -\frac{\text{dim}(\vz)}{2}\log(2\pi) - \frac{1}{2}\vx^\top\vx
\end{align}
$$

### Coding

In Python (using Pytorch) we can code this as follows.

```python
import numpy as np                             
import torch                                   
import torchvision                            
import torchvision.transforms as transforms    
from torch.utils.data import DataLoader        
from torchvision.utils import make_grid       
from torchvision.datasets import MNIST       
import matplotlib.pyplot as plt               
import torch.nn as nn                         
import torch.nn.functional as F                
import torch.optim as optim                   
from torch.distributions import MultivariateNormal
from math import log, pi
from matplotlib.colors import ListedColormap

# Neural Network Architecture               
e_hidden = 500       
d_hidden = 500        
latent_dim = 2
K = 3                    # Normalizing Flow depth

# Optimizer 
learning_rate = 0.001 
weight_decay = 1e-5 

# Learning
epochs = 100
batch_size = 100

# Use GPU/CPU
device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")

## Prepare data
t = transforms.Compose([transforms.ToTensor()])

# Use transformation for both training and test set
trainset = torchvision.datasets.MNIST(root='./data', train=True, download=True, transform=t)
testset  = torchvision.datasets.MNIST(root='./data', train=False, download=True, transform=t)

# Load train and test set
trainloader = DataLoader(trainset, batch_size=batch_size, shuffle=True)
testloader  = DataLoader(testset, batch_size=batch_size, shuffle=True)

## Main VAE class
class VAE_PF_amortized(nn.Module):
    def __init__(self, e_hidden, d_hidden, latent_dim, K=K):
        """VAE with Planar Flows - Define layers of the architecture."""
        super(VAE_PF_amortized, self).__init__()
        # Encoding Layers
        self.e_input2hidden = nn.Linear(in_features=784, out_features=e_hidden)
        
        # mu and log var for q_0 (latent space)
        self.e_hidden2mean = nn.Linear(in_features=e_hidden, out_features=latent_dim)
        self.e_hidden2logvar = nn.Linear(in_features=e_hidden, out_features=latent_dim)
        
        # w, u, b for normalizing flows
        self.e_hidden2w = nn.Linear(in_features=e_hidden, out_features=latent_dim*K) 
        self.e_hidden2u = nn.Linear(in_features=e_hidden, out_features=latent_dim*K) 
        self.e_hidden2b = nn.Linear(in_features=e_hidden, out_features=K)
        
        # Decoding Layers
        self.d_latent2hidden = nn.Linear(in_features=latent_dim, out_features=d_hidden)
        self.d_hidden2image = nn.Linear(in_features=d_hidden, out_features=784)
        
        # Store setting
        self.K = K
        self.latent_dim = latent_dim
        
    def encode(self, x):
        """Maps a data batch (batch_size, 784) to q_0 parameters (mu, logvar) and planar flow 
        parameters (w, u, b)."""
        x = F.relu(self.e_input2hidden(x))
        
        # mu, sigma for latent space
        mu, logvar = self.e_hidden2mean(x), self.e_hidden2logvar(x)
        
        # parameters for normalizing flow
        w, u, b = self.e_hidden2w(x), self.e_hidden2u(x), self.e_hidden2b(x)
        
        # Reshape to facilitate dot products later on
        batch_size = x.size(0)
        w = w.view(batch_size, self.K, 1, latent_dim)
        u = u.view(batch_size, self.K, latent_dim, 1)
        b = b.view(batch_size, self.K, 1, 1)
        return mu, logvar, w, u, b
    
    def flow(self, z0, w, u, b):
        """Describes how a latent sample z_0 ~ N(mu, logvar) gets transformed by a sequence of K planar
        flows into z_K."""
        # Compute batch_size so that this works also at test time
        bs = z0.size(0)     # batch size. At training time is batch_size, at test time number of images
        
        z_k = z0                        # (batch_size, 2, 1)               
        ladj_sum = torch.zeros((bs, 1)) # (batch_size, 1)
         
        for k in range(self.K):
            # Grab parameters for this flow
            w_k = w[:, k, :, :]   # (batch_size, 1, 2)
            u_k = u[:, k, :, :]   # (batch_size, 2, 1)
            b_k = b[:, k, :, :]   # (batch_size, 1, 1)
                        
            # Compute uhat to make f() invertible
            uw = torch.bmm(w_k, u_k)   # (batch_size, 1, 1)
            m_uw = -1 + F.softplus(uw) # size (batch_size, 1, 1)
            uhat_k = u_k + ((m_uw - uw)* w_k.transpose(2, 1) / (torch.norm(w_k, dim=2, keepdim=True)**2))

            # Compute z_{k+1} = f(z_k)
            wz_plus_b = torch.bmm(w_k, z_k) + b_k
            z_k_plus_1 = (z_k + uhat_k * torch.tanh(wz_plus_b)).squeeze(2) # (batch_size, 2, 1) --> (batch_size, 2)
            #print("z_{k+1}: ", z_k_plus_1.size())
            # Compute Log-Absolute-Determinant-Jacobian & add it to running sum
            h_prime = (1 - torch.tanh(wz_plus_b)**2)
            ladj = -(1 + torch.bmm(h_prime*w_k, uhat_k)).abs().add(1e-8).log().squeeze(2)
            ladj_sum += ladj

            # Set z_k <- z_{k+1}
            zk = z_k_plus_1
        
        z0, z_k = z0.squeeze(2), z_k.squeeze(2)
        return z0, zk, ladj_sum
    
    def decode(self, z0):
        """Decodes a latent sample z0 by first feeding through the Planar Flows to obtain z_K and
        then feeding it through the decoder NN to obtain the mean reconstruction.
        NOTE: This should only be used at TEST TIME not at training time."""
        # Need (w, u, b) for the flow but when generating images. Samples them randomly
        bs = z0.size(0)   # Batch size for test set
        u = torch.randn((bs, 3, self.latent_dim, 1))
        w = torch.randn((bs, 3, 1, self.latent_dim))
        b = torch.randn((bs, 3, 1, 1))
        
        # Transform via a Normalizing Flow
        z0, z_k, ladj_sum = self.flow(z0.unsqueeze(2), w, u, b)
        
        # Decode z_K to a mean reconstruction
        return torch.sigmoid(self.d_hidden2image(torch.relu(self.d_latent2hidden(z_k))))
        
        
    def forward(self, x):
        """Describes the forward process of VAE+PF."""
        # Shape Flatten image to [batch_size, input_features]
        x = x.view(-1, 784)
        
        mu, logvar, w, u, b = self.encode(x)  # (batch_size, latent_dim)
        
        # Sample z0 from latent space using mu and logvar. Will have dimensions (batch_size, latent_dim, 1)
        if self.training:
            z0 = torch.randn_like(mu).mul(torch.exp(0.5*logvar)).add_(mu).unsqueeze(2) 
        else:
            z0 = mu.unsqueeze(2)
        
        # Feed z_0 through NF to get z_K
        z0, z_k, ladj_sum = self.flow(z0, w, u, b) # (batch_size, 2), (batch_size, 2), (batch_size, 1)
        
        # Feed z_K through Decoder to get mean reconstruction
        recon = torch.sigmoid(self.d_hidden2image(torch.relu(self.d_latent2hidden(z_k))))
        
        
        # KL = log_q0 + LADJ - log_pK
        log_q0 = -log(2*pi) -logvar.add(1e-8).sum(dim=1, keepdim=True) - torch.bmm(((z0 - mu).add(1e-8)/(2*torch.exp(logvar))).unsqueeze(1), (z0-mu).unsqueeze(2)).squeeze(2)
        # Batch multiplication: (batch_size, 1, 2) and (batch_size, 2, 1) gives (batch_size, 1, 1).
        log_pK = -log(2*pi) -0.5*torch.bmm(z_k.unsqueeze(1), z_k.unsqueeze(2)).squeeze(2).add(1e-8)  # (batch_size, 1)
        # return reconstruction and terms for kl divergence
        return recon, log_q0, ladj_sum, log_pK
```


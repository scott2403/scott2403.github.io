---
title: Improving a Variational Autoencoder with Normalizing Flows
linktitle: VAE with NF
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  normalizing-flows:
    parent: 3) Application to VAEs
    weight: 7

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 7
---

In order to fully grasp the concepts explained here, I strongly recommend you to read my three posts on Variational Autoencoders (in the following order)

* [Variational Autoencoders and the Expectation Maximization Algorithm](http://127.0.0.1:4321/post/variational-auto-encoders-and-the-expectation-maximization-algorithm/)
* [Minimalist Variational Autoencoder in Pytorch with CUDA GPU](http://127.0.0.1:4321/post/minimalist-variational-autoencoder-in-pytorch-with-cuda-gpu/)
* [Assessing a Variational Autoencoder on MNIST using Pytorch](http://127.0.0.1:4321/post/assessing-a-variational-autoencoder-on-mnist-using-pytorch/).

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
$$

### Theory of Vanilla VAEs
Recall that in a Vanilla VAE we feed $\bf{x}$ into an encoder neural network and obtain $(\boldsymbol{\mu}, \log\boldsymbol{\sigma})$. These are the _amortized_ parameters of our approximate posterior distribution

$$
q_{\vphi}(\vz \mid \vx) = \N(\vz \mid \vmu\_{\vphi}(\vx), \text{diag}(\vsigma^2\_{\vphi}(\vx)))
$$

To get a latent sample $\vz \sim \encoder$ we need to use the _reparametrization trick_. This requires sampling $\vepsilon \sim \snd$ and then scaling it and shifting it according to the output of the neural network

$$
\vz = \muz + \sigmaz \odot \vepsilon.
$$

To learn the parameters of our neural network our aim is to maximize the ELBO

$$
\elbo = \eencoder{\log \decoder} - \kl{\encoder}{\prior}
$$

The reconstruction error (the first term) is easy to compute in the Normal and Bernoulli case. In what follows, we will assume that the likelihood is a product of Bernoullis. This is the usual set-up when working with MNIST. The likelihood is then

$$
\decoder = \prod^{\text{dim}(x)}\_{i=1} p_i^{x_i}(1 - p_i)^{1 - x_i}
$$

where $\vect{p} = (p\_1, \ldots, p\_{\text{dim}(x)})^\top$ is the output of the decoder network, and $\vect{p}\in [0, 1]^{\text{dim}(x)}$. The reconstruction error can then be written as

$$
\begin{align}
    \eencoder{\log \decoder}
    &=  \eencoder{\log \prod^{\text{dim}(\vx)}\_{i=1} p_i(\vz)^{x_i}(1 - p_i(\vz))^{1-  x_i}} \newline
    &= \eencoder{\sum^{\text{dim}(\vx)}\_{i=1} x_i \log p_i(\vz) + (1 - x_i) \log(1 - p_i(\vz))}\newline
    &\approx \sum^{n\_{z}}\_{j=1} \sum^{\text{dim}(\vx)}\_{i=1} x_i \log p_i(\vz) + (1 - x_i) \log(1 - p_i(\vz)) \qquad \vz^{(j)} \sim \encoder
\end{align}
$$

where $n\_{\vz}$ is the number of samples that we sample from $\encoder$. Usually we simply set $n\_{\vz} = 1$. This means we only sample one latent variable per datapoint. The objective function to _minimize_ (I have flipped the sign) is therefore

$$
\begin{align}
  -\elbo &= -\sum^{\text{dim}(\vx)}\_{i=1} x_i \log p_i(\vz) + (1 - x_i) \log(1 - p_i(\vz)) - \frac{1}{2}\sum^{\text{dim}(\vz)}\_{j=1} \left(1 + \log\sigma^2\_j - \mu^2\_j - \sigma^2\_j\right) \newline
  &= \text{BCE}(\vect{p}, \vx) - \frac{1}{2}\sum^{\text{dim}(\vz)}\_{j=1} \left(1 + \log\sigma^2\_j - \mu^2\_j - \sigma^2\_j\right)
\end{align}
$$

Using Pytorch we can code it as

```python
def vae_loss(image, reconstruction, mu, logvar):
  """Loss for the Variational AutoEncoder."""
  # Compute the binary_crossentropy.
  recon_loss = F.binary_cross_entropy(
      input=reconstruction.view(-1, 28*28),    # input is p(z) (the mean reconstruction)
      target=image.view(-1, 28*28),            # target is x   (the true image)
      reduction='sum'                          
  )
  # Compute KL divergence using formula (closed-form)
  kl = -0.5 * torch.sum(1 + logvar - mu.pow(2) - logvar.exp())
  return reconstruction_loss + kl
```

### VAE with Normalizing Flows
This time, we not only want our encoder to output $(\vmu, \log \vsigma)$ to shift and scale $\vepsilon\sim \snd$. We also want to feed 

$$\N(\vz \mid \muz, \text{diag}(\sigmaz))$$

through a series of $K$ transformations each one of them depending on a set of parameters $\vlambda_k$. Denoting $\vlambda = (\vlambda_1, \ldots, \vlambda_K)$ we essentially want our Encoder to work as follows:

$$
\vx \longrightarrow \text{Encoder} \longrightarrow (\vmu, \log \vsigma, \vlambda_1, \ldots, \vlambda_K) = (\vmu, \log \vsigma, \vlambda)
$$

Then we would firstly use $(\vmu, \log \vsigma)$ to compute $\vz_0$ using the reparametrization trick
$$
\vz_0 = \vmu + \vsigma \odot \vepsilon \qquad \vepsilon \sim \snd
$$

and finally we would feed $\vz_0$ into the series of transformations to reach the final $\vz_K$

$$
\vz_K = f_K \circ f\_{K-1} \circ \ldots \circ f_2 \circ f_1 (\vz_0).
$$

This means that our approximating distribution is not 

$$
\encoder  = \N(\vz \mid \muz, \text{diag}(\sigmaz))
$$

anymore but, rather, it can be found using the usual change of variable formula

$$
\log \encoder = \log q\_K(\vz_K) = \log q\_0(\vz_0) - \sum^{K}\_{k=1} \ln \left|\text{det}\frac{\partial f\_k}{\partial \vz\_{k-1}}\right|
$$

where the base distribution $q_0(\vz_0)$ is the old approximate posterior distribution
$$
q_0(\vz_0) = \N(\vz_0 \mid \muz, \text{diag}(\sigmaz)).
$$

Thanks to the law of the unconscious statistician we have

$$
\begin{align}
  \elbo 
  &= \eencoder{\log \decoder}-\kl{\encoder}{\prior} \newline
  &= \Eqk{\log p\_{\vtheta}(\vx \mid \vz\_K)} - \Eqk{\log q\_K(\vz\_K) - \log p(\vz\_K)} \newline
  &= \Ebb\_{q\_0(\vz\_0)}[\log p\_{\vtheta}(\vx \mid \vz\_K)]- \Ebb\_{q\_0(\vz\_0)}[\log q\_K(\vz\_K) - \log p(\vz_K)]
\end{align}
$$

As usual, we can approximate this using Monte Carlo and generally we only need one sample. By drawing $\vz_0\sim q_0(\vz_0) = \N(\vmu, \text{diag}(\vsigma))$ we can approximate the ELBO as follows

$$
\begin{align}
  \elbo 
  &\approx \left[\sum^{\text{dim}(\vx)}\_{i=1} x\_i\log p\_i(\vz\_K) + (1 - x\_i)\log(1 - p\_i(\vz\_K))\right] - \log q_K(\vz_K) + \log p(\vz_K).
\end{align}
$$

This means that our objective function is given by
$$
\begin{align}
  -\elbo 
  &= \text{BCE}(\vect{p}, \vx) + \log q_0(\vz_0) + \text{LADJ} - \log p(\vz_K)
\end{align}
$$

where the Log-Absolute-Determinant-Jacobian is the usual
$$
\text{LADJ} = \sum^{K}\_{k=1} \ln \left|\text{det}\frac{\partial f\_k}{\partial \vz\_{k-1}}\right|
$$

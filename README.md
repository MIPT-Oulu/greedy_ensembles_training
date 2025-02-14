# Greedy Bayesian Posterior Approximation with Deep Ensembles

This repository is the official implementation of [Greedy Bayesian Posterior Approximation with Deep Ensembles](https://openreview.net/forum?id=P1DuPJzVTN) by A.
Tiulpin and M. B. Blaschko. (2022). Published in the Transactions on Machine Learning Research
```
@article{
tiulpingreedy,
title={Greedy Bayesian Posterior Approximation with Deep Ensembles},
author={Aleksei Tiulpin and Matthew B. Blaschko},
journal={Transactions on Machine Learning Research},
year={2022},
url={https://openreview.net/forum?id=P1DuPJzVTN},
note={}
}
```
## TL;DR
We propose a novel principled method to approximate Bayesian posterior in Deep Learning via greedy minimization of an f-divergence in the function space, and derive a theoretically justified diversity term.

<center>
<img src="assets/main_figure.png" width="800"/> 
</center>


## Installation

In the root of the codebase:

```
conda env create -f env.yaml
conda activate grde
pip install -e .
```

## Results
We conducted our main evaluations on 3 architectures: PreResNet164, VGG16BN, and WideResNet28x10. 
LSUN and SVHN datasets were used as out-of-distribution. The following table illustrates the main results of the paper on PreResNet164:
</br>
</br>

<table class="tg">
<thead>
  <tr>
    <th rowspan="2">Dataset</th>
    <th rowspan="2">Method</th>
    <th class="tg-7btt" colspan="2">SVHN</th>
    <th class="tg-7btt" colspan="2">LSUN</th>
  </tr>
  <tr>
    <td >AUC</td>
    <td >AP</td>
    <td >AUC</td>
    <td >AP</td>
  </tr>
</thead>
<tbody>
  <tr>
    <td  rowspan="2">CIFAR10</td>
    <td >Deep Ensembles</td>
    <td >0.94</td>
    <td >0.96</td>
    <td >0.93</td>
    <td >0.89</td>
  </tr>
  <tr>
    <td >Ours</td>
    <td><b>0.95</b></td>
    <td><b>0.97</b></td>
    <td><b>0.95</b></td>
    <td><b>0.94</b></td>
  </tr>
  <tr>
    <td  rowspan="2">CIFAR100</td>
    <td >Deep Ensembles</td>
    <td >0.79</td>
    <td >0.88</td>
    <td >0.86</td>
    <td >0.81</td>
  </tr>
  <tr>
    <td>Ours</td>
    <td><b>0.82</b></td>
    <td><b>0.90</b></td>
    <td><b>0.87</b></td>
    <td><b>0.85</b></td>
  </tr>
</tbody>
</table>


## Reproducing the results: training

### CIFAR
We ran our main experiments for ensembles of size 11 on 400 Nvidia V100 GPUs 
(thanks to [Aalto Triton](https://scicomp.aalto.fi/triton/) and [CSC Puhti](https://docs.csc.fi/computing/overview/) clusters). We launched 1 experiment (i.e.
ensemble) per GPU. One can try to re-run our codes on a single-gpu machine using the script located in the `experiments/replicate.sh`. 
It is possible to check the performance for some individual setting with a single seed as follows (must be run from `experiments/`):

* PreResNet164 on CIFAR10:
```
python -m gde.train \
        experiment=cifar_resnet \
        model.name=PreResNet164 \
        data.num_classes=10 \
        ensemble.greedy=true \
        ensemble.ens_size=11 \
        ensemble.diversity_lambda=3 
```

* VGG16BN on CIFAR10
```
python -m gde.train \
        experiment=cifar_vgg \
        model.name=VGG16BN 
        data.num_classes=10 \
        ensemble.greedy=true \
        ensemble.ens_size=11 \
        ensemble.diversity_lambda=5 
```

* WideResNet28x10 on CIFAR10
```
python -m gde.train \
       experiment=cifar_wide_resnet \
       model.name=WideResNet28x10 \
       data.num_classes=10 \
       ensemble.greedy=true \
       ensemble.ens_size=11 \
       ensemble.diversity_lambda=1 
```

To train models on CIFAR100, simply replace `data.num_classes=10` to `data.num_classes=100`,
and `ensemble.diversity_lambda` the values from the paper.

### MNIST and two moons
* MNIST:
```
python -m gde.train experiment=mnist
```
* Two moons
```
python -m gde.train experiment=two_moons_fc_net
```

## Reproducing the results: testing
For convenience, we have provided the script for running standardized evaluation for CIFAR10/100 and MNIST.
To evaluate the results. Assume `experiments/workdir/` contains the snapshots structured into subfolders, then
the following code will run the OOD evaluation, and will create the results stored as pandas dataframes in `experiments`:

```
python -u -m gde.eval_results \
          --arch PreResNet164 \
          --dataset cifar10 \
          --ens_size 11 \
          --seed 5 \
          --workdir workdir/
```

One can loop over seeds to get the results over multiple runs.

## Funding
We acknowledge support from the Research Foundation - Flanders (FWO) through project numbersG0A1319N and S001421N, 
KU Leuven Internal Funds via the MACCHINA project, and funding fromthe Flemish Government under the “Onderzoeksprogramma Artificiële Intelligentie (AI) Vlaanderen"programme.

This research was also supported by strategic funds of the University of Oulu, Finland. 
We thank CSC -- Finnish Center for Science for generous computational resources. 
We also acknowledge the computational resources provided by the Aalto Science-IT project.

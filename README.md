# BEANIE: group Biology EstimAtion iN sIngle cEll

BEANIE is a python package for differential expression analysis in single cell RNA-seq data. It estimates group-level biology between two treatment groups by identifying statistically robust gene signatures. It uses a subsampling based approach to account for over-representation of biology due to differing numbers of cells per sample, and sample-exclusion based approach to reduce the effect of sample biases in differential expression analysis. 

## Installation

To install via github:

```
pip install git+https://github.com/sjohri20/beanie.git
```

## Software Requirements

python v3.6 (it does not work on python versions higher that 3.8 for now)

www.github.com/sjohri20/beanie/wiki


#### Differential Expression

*This is a time consuming step, and may take a few hours depending on the size of your dataset* 

Beanie calculates Differential Expression by using a combination of sample exclusion, subsampling and Mann Whitey U tests. To remove the effects of patient specific biology, the data is divided into folds such that in every fold one patient (from either group) is excluded from the analysis. The number of folds is equal to the number of patients in the cohort. To take into account the cell number differences across patients, equal number of cells are subsampled from from every patient, and this process is repeated multiple times to ensure statistical stability of results). For each subsample, a mann whitney test is performed to calculate the p-value. The p values from all subsamples are combined into a single value using measures of central tendency. Robustness of p values (i.e. quantitative measure of whether a signature is driven by a particular patient's biology) is also calculated at this step.

Beanie then uses a background distribution of random signatures to correct for p value inflation in the previous step. This step leads to accurate estimation of real p-value for the signature.
This step cannot be done if the user provides a signature score file because the random signatures have to be scored by the same method as the real signatures to correct for p values properly. Hence, it is recommended that Beanie's inbuilt scoring methods be used.

```
bobj.DifferentialExpression()
bobj.GetDifferentialExpressionSummary()
```

#### Gene Importance Ranking

Beanie can rank genes by their importance in calculation of score. This step is particularly useful for users when they have very large number of genes in a signature and want to know relative importance of genes.

```
bobj.DriverGenes()
bobj.GetDriverGenesSummary()
```

#### Visualisation

Beanie has many functions to aid the user to visualise the results.

Plot 1: Bar Plot
All signatures which have significant p values are shown in this plot, with the height of the bar being the log(p). Robustness and directionality are also shown in the plot.

```
beanie_obj_melanoma.BarPlot()
```

Plot 2: Dropout Plot
This plot is specially for visualising the non-robust signatures in detail. It shows which patients led to the non-robustness of a particular signature, and also how many signatures became non-robust because of one particular patient. This is important to find patient outliers whose biology is very different from the other samples in the dataset.

```
beanie_obj_melanoma.PatientDropoutPlot()
```

Plot 3: Heatmap
This plot shows the top genes for each signature. By default it plots for 5 signatures which have lowest corrected p-values, but it is recommended that the user uses this plot to visualise for other signatures too. A list of signature names to be plotted can be passed as a parameter to this function.

```
bobj.HeatmapDriverGenes(signature_names = ["signature_1", "signature_2", "signature_3", "signature_4", "signature_5"])
```

Plot 4: Upset Plots
This plot is used for visualising the overlap between the top 10 ranked genes across different signatures. A list of signature names can be provided. If no list is provided, by default, the top 5 signatures with lowest p values are used for plotting.

```
bobj.UpsetPlotDriverGenes(signature_names=["signature_1", "signature_2", "signature_3", "signature_4", "signature_5"])
```

Similarly, overlap between the all genes for signatures can also be seen. A list of signature names can be provided. If no list is provided, by default, the top 5 signatures with lowest p values are used for plotting.

```
bobj.UpsetPlotSignatureGenes(signature_names=["signature_1", "signature_2", "signature_3", "signature_4", "signature_5"])
```




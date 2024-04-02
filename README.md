# ICA_Epigenetics

This project investigates the efficacy of Independent Component Analysis (ICA), an unsupervised learning algorithm, in predicting biological traits using methylation data. Despite ICA's established success in signal processing, particularly in neuroscience, its application in computational biology, specifically in epigenetics, has been limited.The study uses independent components derived from a bootstrapped ICA pipeline to analyze the correlation between methylation sites and individual traits. While the ICA pipeline identified important methylation sites, it was not useful for predicting traits using Lasso regression. The ICA model’s  performance was comparable to, or slightly worse than, Principal Component Analysis (PCA) and Partial Least Squares (PLS). The study highlights the challenges associated with ICA's application to static datasets, and the method's overall inferiority in comparison to established alternatives. The results suggest that ICA may not be a suitable method for processing methylation datasets or constructing predictive models for biological traits.
Keywords: Independent Component Analysis, methylation, biological traits, computational biology, predictive modeling, Principal Component Analysis, Partial Least Squares, epigenetics.


## Introduction

Independent Component Analysis is an unsupervised learning algorithm developed primarily by the Finnish computer scientist Aapo Hyvärinen. Since its creation, ICA has been a powerful tool in the field of signal processing. ICA is commonly used in neuroscience, but it is unclear why other areas of computational biology have not adopted it. Computational epigenetics frequently uses PCA and PLS to process or preprocess data sets. However, ICA has been largely neglected by the field. ICA has traditionally been used for processing time series data such as EEG, fMRI, and acoustics [2].
ICA separates signals by assuming non-gaussianity to find a linear representation that makes the signals as statistically independent as possible [1]. ICA seeks to minimize mutual information between each Independent Component (IC). The most basic application of this is illustrated by “The Cocktail Party Problem.” If a single microphone was placed in a crowded room, the sound signal would contain signals from many sources. ICA can be used to separate the individual sources to reproduce a single speaking voice. 
Epigenetics is a quickly growing field of study, but it has lagged behind genetic studies due to its more recent discovery. Changes to DNA methylation can be caused by environmental factors as well as natural processes like aging, making methylome analysis incredibly important. By extension, the methods by which the methylome is analyzed are also important. By treating DNA methylation as a signal, it is possible to use ICA to glean insight into its impact on health outcomes. The goal of this project was to show that ICA can be used to identify groups of highly influential methylation sites that modulate biological traits.

 
## Results

Trait Correlation
Stable independent components were used to make a correlation matrix (Fig. 1) with the quantified traits of the individuals. The independent components are the centrotypes of the bootstrapped ICA pipeline. Each component is maximally independent from the others, and using the components associated with the unmixing matrix revealed methylation sites that were more strongly associated with individual traits. IC5, IC6, and IC8 were further examined because of their strong correlation.


<img width="699" alt="image" src="https://github.com/Adronz/ICA_Epigenetics/assets/33525795/296321be-5635-4b1c-abaa-10b86978cedb">

Fig 1. IC x trait correlation matrix
Potentially important methylation sites were found by iterating through the contribution weights of the component’s unmixing matrix. A shortened list was created by removing methylation sites that did not have a contribution of greater than 0.002. IC5 had 30 such sites, IC6 had 159, and IC8 had 107. The identified methylation sites were analyzed using Cistrome-Go to elucidate the associated genetic and metabolic pathways. The pipeline found meaningful pathways from methylation data, which could be further analyzed in another experiment. 

Model Prediction

The stable independent components were inputted into a Lasso regression model to be compared to other data processing methods. To prevent overfitting, a leave-one-out cross-validation protocol was used to make models that were trained on all but one sample. The model showed mixed results, as it had trouble predicting some biomarkers over others. This could be due to a number of reasons, including the compactness of the cluster the component came from, and its relative isolation from other groups. 
However, the model did not perform impressively when compared to models using PCA or PLS. The ICA model performed as well as or slightly worse than the PCA and PLS models. This was measured by comparing the Mean Absolute Error (MAE) and R2 values of ICA, PCA, and PLS models for each trait (Table 1).

<img width="862" alt="image" src="https://github.com/Adronz/ICA_Epigenetics/assets/33525795/abad37fa-4336-46d8-9da1-9f9137e87253">

Table 1 Table of Mean Absolute Error and R2 values for ICA, PCA, and PLS models for predicting each trait.

The MAE of ICA is reliably within the same order as PCA and PLS. However, the R2 of ICA is consistently lower, with the exception of the ICA model’s R2 being greater than the PCA model’s R2 for Neutrophils. Unfortunately, outperforming the established methods for a single trait does not validate the use of ICA for regression modeling. 
While the model was able to predict some traits to a moderate degree, the other two methods proved to be more effective. Because ICA is traditionally used on time series data, the data needed far more pre-processing and the pipeline in its current form requires more work from the user. This, paired with the variable quality of the component clusters and unimpressive predictive power makes ICA a poor method for processing methylation data.



## Methods

Data 

The ICA program was tested using a methylation matrix, and a matrix of traits associated with those individuals. The methylation matrix was composed of lung and kidney samples (n=153) and related to the percentage of methylation of targeted sites (n=13770). A dataset containing biomarkers for each individual was provided in conjunction with the methylation data.

## Model
The ICA model was based on the FastICA function from the scikit-learn Python library. Because ICA is an unsupervised learning algorithm with a random state, the model was fitted multiple times using a bootstrapping method. The unmixing matrices from each iteration were stored for later analysis.

Clustering

Each iteration of the ICA algorithm was analyzed and clustered by the dissimilarity, , of its components using hierarchical clustering methods provided by the scipy library. The data was further organized by grouping independent components by how dissimilar they are from all the other components using the fcluster function and the ‘distance’ criterion. For each cluster, the centrotype was calculated by finding the component that is the most similar to the other components in its cluster.  
To determine cluster quality, each cluster received a score between zero and one based on its compactness and isolation from other clusters. Scores below a chosen threshold (s=0.9) were considered ‘unstable.’ Centrotypes from ‘stable’ clusters were considered to be representative samples of highly reproducible independent components. 

<img width="642" alt="image" src="https://github.com/Adronz/ICA_Epigenetics/assets/33525795/6ac54026-ef52-4a33-9fc4-d23648ac7395">

Fig. 2 Depicts clusters sorted by scores. The first twelve clusters are considered to be stable. 

Trait correlation

The impact of each stable component on individual traits was measured using a correlation matrix. To analyze the weighted contribution of individual methylation sites on highly correlated traits, the centrotype of the correlated ‘stable’ cluster was matched to its unmixing matrix. Sites were added to a list if their weighted contribution was greater than an arbitrary threshold. This threshold was tuned to isolate increasingly important methylation sites. 


Predictive Power Comparison

The scikit-learn function LassoCV was used to test the predictive power of the ICA components. The results were cross-validated using Leave-One-Out (LOOCV) and the cross_val_score function. From this, the mean absolute error (MAE) and R2 for each trait were calculated. The same procedure was done for Principal Component Analysis (PCA), and a Partial Least Squares Regression was performed. The results of the three methods were then compared in order to determine the efficacy of using ICA to predict biological traits.

## Discussion

The aim of the project was to show that ICA can be used to accurately predict biological traits from a methylation dataset. Since ICA does not prefer any one component, it could have been used alongside more common data science methods such as PCA and PLS to group methylation data in meaningful ways. It was observed that ICA is a viable option on its own for predicting biological traits, and seemed to accurately predict certain markers, such as Neutrophil Bands and the sex of the individual. From this, it was possible to obtain individual contributing methylation sites. However, the model was not useful for predicting the less correlated traits. 
The performance of the ICA model was lackluster. The model performed as well, or worse than the models using PCA and PLS. In addition to the lower accuracy, implementing ICA for static datasets like a methylation matrix is much more involved than using PCA and PLS. The inherent instability of the method also calls into question the quality of the results for less compact clusters. For these reasons, ICA is not a viable method for processing methylation datasets, or for predictive models. 


## References

[1] A. Hyvärinen, E. Oja, (2000) Independent component analysis: algorithms and applications, Neural Networks, Volume 13, Issues 4–5, 411-430

[2] McKeown MJ, Hansen LK, Sejnowsk TJ. Independent component analysis of functional MRI: what is signal and what is noise? Curr Opin Neurobiol. 2003 Oct;13(5):620-9. doi: 10.1016/j.conb.2003.09.012. PMID: 14630228; PMCID: PMC2925426.




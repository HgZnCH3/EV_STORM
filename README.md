# EV_STORM

This is a repository for the code and data of the article "Nanoscale organization of universal extracellular vesicle markers in single vesicles enables cancer discrimination".

## Environment:
- Python 3.10.15  
- SciPy 1.10.0  
- scikit-image 0.25.0  
- scikit-learn 1.5.1  
- imbalanced-learn 0.14.0  
- LightGBM 4.6.0  
- statannotations 0.7.2
- Matplotlib 3.9.2
- NumPy 1.26.4
- Seaborn 0.13.2

## Example
1. Download or clone the repository.
2. Install the dependencies listed in requirements.txt or environment.yml.
3. Open EV_Cluster.ipynb.
4. Set data_dir, save_dir and ML_dir to the local paths containing the processed geometric descriptor data.
5. Run the notebook cells to reproduce nanocluster geometric analysis, machine-learning classification and figure generation.

## Nanocluster analysis
The distribution of surface proteins on single EVs was characterized using the Density-Based Spatial Clustering of Applications with Noise (DBSCAN) algorithm.[^1] By optimizing the neighborhood radius ($eps$) and the minimum number of localization points ($MinPts$), the algorithm effectively identified protein clusters of arbitrary shapes and distinguished specific molecular localizations from random background noise. In this study, the parameters were set to an $eps$ of 40 nm and a $MinPts$ of 5 to ensure accurate detection of protein nanoclusters across all imaging channels. To achieve spatial colocalization of CD63, EpCAM, and CD81 clusters on individual vesicles, a two-step DBSCAN clustering strategy was employed. The three-dimensional centroid coordinates of each protein nanocluster identified in the first round were extracted as a new dataset for a second round of DBSCAN analysis. Nanoclusters separated by a spatial distance of less than 350 nm were assigned to the same EV, and their identities were recorded for subsequent analysis.

## Geometric analysis
The nanoclusters were projected onto 2D for geometric analysis. According to the resulting 2D coordinates, as shown in Supplementary Figure 5, the gaussian kernel density estimation (KDE) was calculated through SciPy (1.10.0). Then, a mask was applied to the KDE to filter out values smaller than threshold pf (pf = 0.25). Subsequently, the geometric parameters were extracted based on the filtered KDE through scikit-image (0.25.0) [^2]. All geometric feature data was calculated based on pixels, where 1 pixel = 4 nm. The normality of the geometric feature data of protein nanoclusters was examed using the scipy.stats.kstest function. The results show that the data does not follow a normal distribution (data not shown). Therefore, Mann Whitney test was used for significance testing between two independent samples and Kruskal-Wallis test was used for multiple (>2) samples. Significance testing and plotting are performed by the statannotations (0.7.2) library [^3].

## Machine Learning Analysis

To address data imbalance, Balanced Random Forest (BalancedRF), LightGBM (LGB) and SVM were chosen for machine learning analysis of nanoclusters’ geometric parameters. The classifiers were established based on Python 3.10.15 using various open libraries: the BalancedRF was from Imbalanced-learn (0.14.0), the SVM with RBF kernel from Scikit-Learn (1.5.1), and lightGBM (4.6.0) [^4] from previous paper. For clinical sample, leave-one-patient-out cross validation (LOPOCV) and bag-level aggregation strategies were applied to improve the classification. Specifically, each patient's data was first divided into subsets according to the specified bagging size parameter, generating a bag-level dataset for each patient. Then, in each round, the data from one patient was used as the test set, while the rest were used for training. The training process was repeated until all patient data have been iterated. In addition, bootstrap was applied to estimate the confidence interval (CI) of the model's performance. In each iteration, s samples (s = original number of patients) were resampled with replacement from the patient pool as the training set; and the remaining patients who were not selected were served as the test set. The bootstrap was repeated 1000 times. 

## REF
[^1]: Khater, I. M.;  Nabi, I. R.; Hamarneh, G., A Review of Super-Resolution Single-Molecule Localization Microscopy Cluster Analysis and Quantification Methods. Patterns 2020, 1 (3), 100038.
[^2]: https://scikit-image.org/docs/stable/api/skimage.measure.html#skimage.measure.regionprops_table.  
[^3]: https://doi.org/10.5281/zenodo.14258156.  
[^4]: Guolin Ke, Qi Meng, Thomas Finley, Taifeng Wang, Wei Chen, Weidong Ma, Qiwei Ye, Tie-Yan Liu. "LightGBM: A Highly Efficient Gradient Boosting Decision Tree." Advances in Neural Information Processing Systems 30 (NIPS 2017), pp. 3149-3157.

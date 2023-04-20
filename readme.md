### 3D Printing Extrusion Detection Project Summary

The objective of the project is participate in the Kaggle competition of Early detection of 3D printing issues, to early detect a common 3D printing error - under extrusion.  3D printing is a process of pushing molten plastic through a small nozzle onto a print bed, the errors are monitored via close-up cameras mounted near the printer nozzle.

#### EDA
Ground Truth 0: no extrusion.  Ground Truth 1: has extrusion.
![image](https://user-images.githubusercontent.com/21034990/230674621-a1049444-0c6c-4d21-91e0-e2080c5aa3aa.png)

#### Architecture
The model is built on the foundation of Resnet32. In order to obtain the best validation F1 score, network assembly, inference resize, L2 regularization, has been combined in place during the experiments.

#### Evaluation matrix

The evaluation of model performance is based on F1 score 

```
F1 = 2 * (precision * recall) / (precision + recall)
```
#### Result

The model has reached a F1 score 0.99954 for training, 0.99659 for validation.

#### Contestant
Kaggle account: kathmanducow


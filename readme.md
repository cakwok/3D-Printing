## 3D Printing Extrusion Detection Project Summary

The goal of the project is to build a model to detect a common error in 3D printing - under extrusion - at an early stage. 

The process of 3D printing involves pushing molten plastic through a small nozzle onto a print bed, and the errors are monitored using close-up cameras mounted near the printer nozzle.

To achieve this goal, our team have utilized the state-of-the-art computer vision techniques to analyze images captured. Our model was trained on a large dataset of 3D printer images to identify the early signs of under extrusion and to alert the user before the printing process goes too far.

This project has the potential to significantly reduce the cost and time associated with 3D printing by reducing the number of failed prints and increasing the overall quality of the final product.

### EDA
The dataset is consist of 77007 images.  Each image corresponds to a particular point in time during the printing process, and is associated with a binary label indicating whether or not under extrusion is present at that time.  The label distribution of positive verus negative samples is around 55:45.

Ground Truth 0: no extrusion.  Ground Truth 1: has extrusion.
<img width="888" alt="image" src="https://user-images.githubusercontent.com/21034990/233508189-34a94c9a-dd10-4f99-a637-27e2c11c25bf.png">
```
Training Dataset size: 77007 

Label Distribution:

1    42165
0    34842
```

### Architecture
The model is built on the foundation of Resnet32.  The challenge of this problem lies not only in performing the classification task accurately but also in overcoming the issue of overfitting. The goal is to achieve the best testing F1 score, which requires careful consideration of various techniques such as regularization, data augmentation, and hyperparameter tuning.  Extensive experimentation has been conducted with network assembly, inference resize, and L2 regularization to optimize the model's performance and ensure that it generalizes well to unseen data.

### Evaluation matrix

The evaluation of model performance is based on F1 score 

```
F1 = 2 * (precision * recall) / (precision + recall)
```
### Result
The training set has resulted in an impressive F1 score of 0.99954, while the validation set has achieved a highly commendable score of 0.99659. These scores indicate that the model is highly accurate and precise in detecting and classifying the targeted issue of under extrusion in 3D printing

### Contestant
Kaggle account: kathmanducow

##### Citation
```
@misc{early-detection-of-3d-printing-issues,
    author = {Kenneth},
    title = {Early detection of 3D printing issues},
    publisher = {Kaggle},
    year = {2023},
    url = {https://kaggle.com/competitions/early-detection-of-3d-printing-issues}
}
```

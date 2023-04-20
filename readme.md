#### 3D Printing Extrusion Detection Project Summary

The objective of the project is participate in the Kaggle competition of Early detection of 3D printing issues, to early detect a common 3D printing error - under extrusion.  3D printing is a process of pushing molten plastic through a small nozzle onto a print bed, the errors are monitored via close-up cameras mounted near the printer nozzle.

Ground Truth 0: no extrusion.  Ground Truth 1: has extrusion.
![image](https://user-images.githubusercontent.com/21034990/230674621-a1049444-0c6c-4d21-91e0-e2080c5aa3aa.png)

#### Evaluation matrix

The evaluation of model performance is based on F1 score 

```
F1 = 2 * (precision * recall) / (precision + recall)
```
#### Performance tuning Journey

1. Baseline: batch size 128, learning rate 0.01, epoch 1 for this larget dataset, pretrained resnet34, momentum 0.5, no scheduler

- Started with 0.01 of the dataset to check setup.  training loss ~0.01 - 0.09 after loading weight of mixing trainings of different dataset sizes, with or without data augmentation.

2. Tuning 1:  resnet34 using paddy disease
- tuned for 1 day with learning rate 0.001, linear scheduler, momentum 0.8, the best training loss obtained is 0.001 - 0.009.  But at the Kaggle leadership dashboard, I can only get a slightly improved F1 score with this tuning.  
- plot performance matrix against step size maybe a better choice than epochs.  with this large dataset it could provide meaninful trainings for even 1 epoch.
- freeze all layers and tuned FC only, seems very effective

3. Tuning 2: resnet101
- Now started resnet101 and considering ensemble with resnet34.  however, after running a whole day for the first epoch, it doesn't look like training loss has reduced.  it was steadily around 0.4xxxx.  
- have tried unfreeze layer4 + FC, and unfreeze FC only, both not converaging
- give up resnet101

4. Tuning 3: weight decay at resnet34
- read from literature that weight decay doesn't help reducing training loss, but would help to reduce overfitting
- have trained the network with 3 full epochs.  overall training loss maintained at 0.00xxx, but at kaggle dashboard, the f1 score showed significant improvemment.  
- not sure if it's because of 3 full epochs, or is it because of the weight decay
- changed new performance matrix - F1 score as per batch update, instead of referencing loss
- decide to further train the same config

```
F1 score
 0.9930986191096485
Train Epoch: 1 [60160/77007 (78%)]	Loss: 0.008596
F1 score
 0.9960260225885226
Train Epoch: 1 [61440/77007 (80%)]	Loss: 0.015763
F1 score
 0.9978043300653594
Train Epoch: 1 [62720/77007 (81%)]	Loss: 0.009114
F1 score
 0.996856789044289
Train Epoch: 1 [64000/77007 (83%)]	Loss: 0.014843
F1 score
 0.9952065295815296
Train Epoch: 1 [65280/77007 (85%)]	Loss: 0.028644
F1 score
 0.9958309052059052
Train Epoch: 1 [66560/77007 (86%)]	Loss: 0.003002
```

5. batch size = 32, resulted in lower f1 score

```
F1 score
 0.9912830225330225
Train Epoch: 3 [34880/77007 (45%)]	Loss: 0.018988
F1 score
 0.9885912698412699
Train Epoch: 3 [35200/77007 (46%)]	Loss: 0.000703
F1 score
 0.9950757575757576
Train Epoch: 3 [35520/77007 (46%)]	Loss: 0.001086
F1 score
 0.9931318681318682
Train Epoch: 3 [35840/77007 (47%)]	Loss: 0.019791
F1 score
 0.9891996891996893
Train Epoch: 3 [36160/77007 (47%)]	Loss: 0.001158
F1 score
 0.9943181818181819
Train Epoch: 3 [36480/77007 (47%)]	Loss: 0.003835
F1 score
 0.9795003607503607
```

6.  batch size 128, no weight decay, training dataset 11000.  resulted better kaggle f1 score, but the loss and f1 score like above, hasn't changed much.

7.  VGG11 lr0.01 momentum0.9 weightdecay0.0005 batch64 epoch15.  gave up ensemble, because both reset132 and vgg11 trainings cannot bring comparable training loss of the prior settings.  continue to tune resnet32.
```
Train Epoch: 14 [960/4053 (23%)]	Loss: 0.769088
F1 score
 0.3607877467712026
Train Epoch: 14 [1920/4053 (47%)]	Loss: 0.782686
F1 score
 0.32338358128799305
Train Epoch: 14 [2880/4053 (70%)]	Loss: 0.797145
F1 score
 0.35004500962681107
Train Epoch: 14 [3840/4053 (94%)]	Loss: 0.773297
Total running time: 99.92383027076721
Training
F1 score
 0.32786105511168395
Train Epoch: 15 [960/4053 (23%)]	Loss: 0.761798
F1 score
 0.3609158169348281
Train Epoch: 15 [1920/4053 (47%)]	Loss: 0.746524
F1 score
 0.40349107833386083
Train Epoch: 15 [2880/4053 (70%)]	Loss: 0.761932
F1 score
 0.36804493169261937
Train Epoch: 15 [3840/4053 (94%)]	Loss: 0.723777
```
8.  Painful experience!!  turn on num_workers=4 all the time!!
test_dataloader = DataLoader(test_dataset, batch_size=BATCH_SIZE, num_workers=8)

9.  checked validation.  4049 the best achieved with validation cost (output(image) + output(flip_image))/2, Apr13

10.  checked transform.compose resize.  the dataset should be 126x224 but previous setting was 224x224.  also cleared and changed  transform.compose sequential features to randomapply transformsations instead.  Have run 10 epochs of full dataset after updating new resolution, but cannot get the same F1 score and training loss.  fallback to 224x224

```
Train Epoch: 10 [64000/77007 (83%)]	Loss: 0.245086
F1 score
 0.9297455161770419
Train Epoch: 10 [65280/77007 (85%)]	Loss: 0.300780
F1 score
 0.91949034804044
Train Epoch: 10 [66560/77007 (86%)]	Loss: 0.297498
F1 score
 0.9160117315792868
Train Epoch: 10 [67840/77007 (88%)]	Loss: 0.274283
F1 score
 0.9436386173058967
Train Epoch: 10 [69120/77007 (90%)]	Loss: 0.423778
F1 score
 0.9181519588458926
Train Epoch: 10 [70400/77007 (91%)]	Loss: 0.238636
F1 score
 0.9246162551194721
Train Epoch: 10 [71680/77007 (93%)]	Loss: 0.288420
F1 score
 0.9148605887576476
Train Epoch: 10 [72960/77007 (95%)]	Loss: 0.137944
F1 score
 0.9151224656164914
Train Epoch: 10 [74240/77007 (96%)]	Loss: 0.514602
F1 score
 0.9203137446703624
Train Epoch: 10 [75520/77007 (98%)]	Loss: 0.173517
F1 score
 0.9268987322205443
Train Epoch: 10 [76800/77007 (100%)]	Loss: 0.125578
```

11.  created 4 transforms, averaged for validation.  4044/4053
```
Validation index: 30 [30/32 (94%)]	Average Loss: 0.006344
F1 score
 0.9978958445546067

Test set: Avg. loss: 0.0000, Accuracy: 4044/4053 (100%)
```

12.  created 2 transforms, averaged for validation.  4042/4053 (100%).  at this snapshot, it is taken as golden config
13.  created 2 transforms, take max for validation.  4045/4053 (100%)
14.  tried full dataset 10 epochs with scheduler
15.  cleared load weight from loading at each epoch to outside the loop.  beforehand, each epoch would loan the best weight or the first weight of the last batch, due to best_weight = 100 and if loss < best_weight then best_weight = loss at each beginning of epochs.  have also clear best_weight = 100 outside the epoch.  have also add a validation(0.5% of whole dataset) after training 1 epoch(95% of whole dataset)

Results still the same.

```

Train Epoch: 4 [70400/77007 (91%)]	Loss: 0.004426
F1 score
 1.0
Train Epoch: 4 [71680/77007 (93%)]	Loss: 0.008380
F1 score
 0.9983630952380953
Train Epoch: 4 [72960/77007 (95%)]	Loss: 0.000474
F1 score
 0.9961037226662227
Train Epoch: 4 [74240/77007 (96%)]	Loss: 0.002010
F1 score
 0.995727362914863
Train Epoch: 4 [75520/77007 (98%)]	Loss: 0.012093
F1 score
 0.9995404411764706
Train Epoch: 4 [76800/77007 (100%)]	Loss: 0.000434
Total running time: 412.9576268196106
Validation index: 10 [10/32 (31%)]	Average Loss: 0.014280
F1 score
 0.9957374222999222
Validation index: 20 [20/32 (62%)]	Average Loss: 0.014876
F1 score
 0.995162909612783
Validation index: 30 [30/32 (94%)]	Average Loss: 0.012604
F1 score
 0.9965892723653479

Test set: Avg. loss: 0.0001, Accuracy: 4038/4053 (100%)
```

action items -
- i have overlooked organizer's reminder to counter overfitting.  maybe tuning 1 has ripped good result already.  so now try deep domain adaptation
- try network ensemble

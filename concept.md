  # FLow

1000 Images

        │
        ▼
Load Dataset

        │
        ▼
Split Dataset

        │
        ├──────────────┐
        ▼              ▼
   Train (800)     Validation (200)

        │
        ▼
Resize

        │
        ▼
Normalize

        │
        ▼
Conv2D

        │
        ▼
ReLU

        │
        ▼
Pooling

        │
        ▼
Conv2D

        │
        ▼
Pooling

        │
        ▼
Flatten

        │
        ▼
Dense

        │
        ▼
Softmax

        │
        ▼
Prediction

        │
        ▼
Loss

        │
        ▼
Backpropagation

        │
        ▼
Update Conv Filters + Dense Weights

        │
        ▼
Next Training Image

        │
        ▼
After 800 Images

        │
        ▼
Validation (Only Prediction)

        │
        ▼
Validation Accuracy

        │
        ▼
Next Epoch


## epochs
means : -- if batch size = 32 
           then in group of 32 image will pass in the cnn and dense neural network ... and one image feature will pass in the 
Epoch 1

Image 1
↓

Learn

Image 2
↓

Learn

...

Image 800
↓

Learn

========================

Epoch 2

Image 1
↓

Already Learned

↓

Improve More

Image 2
↓

Improve More

...

Image 800
↓

Improve More

========================

Epoch 3

Same Images

↓

Even Better Learning

## back propogation 
              Batch Size = 32

Image1  ─┐
Image2  ─┤
Image3  ─┤
...      ├────────► CNN ───────► Dense ─────► Predictions                         
Image32 ─┘

                       │
                       ▼

         32 Predictions + 32 Actual Labels

                       │
                       ▼

                Average Loss

                       │
                       ▼

               Backpropagation

                       │
                       ▼

             Update All Weights

## image features 

Image1 → Features1

Image2 → Features2

...

Image32 → Features32

Ab har image ke features Dense Layer me jaate hain.

Features1  → Prediction1

Features2  → Prediction2

...

Features32 → Prediction32

## flows 

                 Batch = 32 Images

 Image1
 Image2
 Image3
 ...
 Image32
     │
     │
     ▼
 ┌───────────────────┐
 │       CNN         │
 │ (Same Filters)    │
 └───────────────────┘
     │
     ▼
32 Feature Maps
     │
     ▼
Dense Layer
     │
     ▼
32 Predictions
     │
     ▼
32 Individual Losses
     │
     ▼
Average Loss
     │
     ▼
ONE Backpropagation
     │
     ▼
ONE Weight Update
     │
     ▼
Next Batch
  
  ## check tha fitting 

🚀 DECISION FLOW
                Compare Accuracy

                      │
      ┌───────────────┼────────────────┐
      │               │                │
      ▼               ▼                ▼

Train≈Val      Train>>Validation    Train Low
Both High       Validation Low      Validation Low

      │               │                │
      ▼               ▼                ▼

 GOOD FIT      OVERFITTING      UNDERFITTING

 
 ## decision 

                           TRAIN MODEL
                               │
                               ▼
                 Compare Train vs Validation
                               │
          ┌────────────────────┼────────────────────┐
          │                    │                    │
          ▼                    ▼                    ▼

   Train≈Validation     Train>>Validation     Train Low
      Both High          Train High           Validation Low
                          Validation Low

          │                    │                    │
          ▼                    ▼                    ▼

      GOOD FIT          OVERFITTING         UNDERFITTING
          │                    │                    │
          ▼                    ▼                    ▼

      Deploy 🚀        Model Memorized      Model Not Learning

                               │                    │
                               ▼                    ▼

                    Data Augmentation      Increase Epochs
                    (More Variations)      (More Practice)

                               ▼                    ▼

                        Dropout             Bigger Model
                   (Random Neurons OFF)   (More Capacity)

                               ▼                    ▼

                     Early Stopping      Tune Learning Rate
                  (Stop Before Overfit)  (Correct Learning Speed)

                               ▼                    ▼

                  Batch Normalization      More Data
                     (Stable Training)   (More Examples)

                               ▼                    ▼

                  L2 Regularization   Better Architecture
                  (Penalty on Weights) (ResNet/EfficientNet)

                               ▼                    ▼

                  Reduce Model Size    Transfer Learning
                   (Less Memorization) (Pretrained Knowledge)

                               ▼
                         Transfer Learning

                               ▼

                          Train Again

                               ▼

                           Deploy 🚀
  
  
  
  ## overfitting 
                           TRAIN MODEL
                                │
                                ▼
                 Check Train vs Validation Accuracy
                                │
                                ▼
                    ┌─────────────────────┐
                    │ Train >> Validation │
                    │ (99% vs 75%)        │
                    └─────────────────────┘
                                │
                                ▼
                         OVERFITTING ?
                                │
               ┌────────────────┴────────────────┐
               │ Yes                             │ No
               ▼                                 ▼
   Model ne Training Data Yaad Kar Li      UNDERFITTING Check
               │
               ▼
┌──────────────────────────────────────────────────────────────────────────────┐
│ 1. Data Augmentation   → Same image ko Rotate/Flip/Zoom karke               │
│                           naye images banata hai.                           │
├──────────────────────────────────────────────────────────────────────────────┤
│ 2. Dropout            → Random neurons OFF karta hai taaki                  │
│                           model memorize na kare.                           │
├──────────────────────────────────────────────────────────────────────────────┤
│ 3. Early Stopping     → Validation performance girte hi                     │
│                           training rok deta hai.                            │
├──────────────────────────────────────────────────────────────────────────────┤
│ 4. Batch Normalization→ Layer output normalize karta hai,                   │
│                           training stable hoti hai.                         │
├──────────────────────────────────────────────────────────────────────────────┤
│ 5. L2 Regularization  → Bade weights ko penalty deta hai                    │
│                           taaki model simple rahe.                          │
├──────────────────────────────────────────────────────────────────────────────┤
│ 6. Reduce Model Size  → Layers/neurons kam karke                            │
│                           memorization power kam karta hai.                 │
├──────────────────────────────────────────────────────────────────────────────┤
│ 7. More Data          → Zyada images dekar memorization                     │
│                           kam karta hai.                                    │
├──────────────────────────────────────────────────────────────────────────────┤
│ 8. Transfer Learning  → Pretrained model use karta hai                      │
│                           jo better generalize karta hai.                   │
└──────────────────────────────────────────────────────────────────────────────┘
                                │
                                ▼
                         Train Again
                                │
                                ▼
                    Validation Improved ?
                                │
                      Yes ─────────► Deploy 🚀

## underfitting 
                          TRAIN MODEL
                               │
                               ▼
                Check Train vs Validation Accuracy
                               │
                               ▼
                   ┌────────────────────┐
                   │ Train Low          │
                   │ Validation Low     │
                   │ (60% vs 58%)       │
                   └────────────────────┘
                               │
                               ▼
                       UNDERFITTING ?
                               │
               ┌───────────────┴────────────────┐
               │ Yes                            │ No
               ▼                                ▼
      Model Seekh Hi Nahi Pa Raha          GOOD FIT
               │                                │
               ▼                                ▼
┌──────────────────────────────────────────────────────────────────────────────┐
│ 1. Increase Epochs     → Model ko aur practice karne do.                    │
├──────────────────────────────────────────────────────────────────────────────┤
│ 2. Bigger Model        → Aur Conv/Dense layers add karo                     │
│                           taaki complex patterns seekhe.                    │
├──────────────────────────────────────────────────────────────────────────────┤
│ 3. Tune Learning Rate  → Learning speed ko optimize karo.                   │
├──────────────────────────────────────────────────────────────────────────────┤
│ 4. More Data           → Zyada examples se better learning hoti hai.        │
├──────────────────────────────────────────────────────────────────────────────┤
│ 5. Better Architecture → ResNet / EfficientNet jaise                        │
│                           better CNN architecture use karo.                 │
├──────────────────────────────────────────────────────────────────────────────┤
│ 6. Transfer Learning   → Pretrained model ka knowledge use karo.            │
└──────────────────────────────────────────────────────────────────────────────┘
                               │
                               ▼
                         Train Again
                               │
                               ▼
                    Accuracy Improved ?
                               │
                      Yes ─────────► Deploy 🚀

## one page decision 

                          TRAIN MODEL
                               │
                               ▼
                 Compare Train vs Validation
                               │
          ┌────────────────────┼────────────────────┐
          │                    │                    │
          ▼                    ▼                    ▼

   Train≈Validation     Train>>Validation     Train Low
      Both High          Train High           Validation Low
                          Validation Low

          │                    │                    │
          ▼                    ▼                    ▼

      GOOD FIT          OVERFITTING         UNDERFITTING
          │                    │                    │
          ▼                    ▼                    ▼

      Deploy 🚀        Model Memorized      Model Not Learning

                               │                    │
                               ▼                    ▼

                    Data Augmentation      Increase Epochs
                    (More Variations)      (More Practice)

                               ▼                    ▼

                        Dropout             Bigger Model
                   (Random Neurons OFF)   (More Capacity)

                               ▼                    ▼

                     Early Stopping      Tune Learning Rate
                  (Stop Before Overfit)  (Correct Learning Speed)

                               ▼                    ▼

                  Batch Normalization      More Data
                     (Stable Training)   (More Examples)

                               ▼                    ▼

                  L2 Regularization   Better Architecture
                  (Penalty on Weights) (ResNet/EfficientNet)

                               ▼                    ▼

                  Reduce Model Size    Transfer Learning
                   (Less Memorization) (Pretrained Knowledge)

                               ▼
                         Transfer Learning

                               ▼

                          Train Again

                               ▼

                           Deploy 🚀
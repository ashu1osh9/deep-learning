# 🔆 Solar Panel Classification — Detailed Explanation

> Yeh document notebook ke har part ko **detail mein** explain karta hai — taaki aap khud samajh sakein code kya kar raha hai, kyun kar raha hai, aur kaha kya ho raha hai.

---

## 📑 Table of Contents

- Project Overview
- Dataset Structure
- Setup & Data Loading
- Model 1: Basic CNN
- Model 2: Overfitting Fix
- Model 3: Advanced CNN + Augmentation
- Model 4: MobileNetV2 (Transfer Learning)
- Model 5: EfficientNetB0 (Transfer Learning)
- Hyperparameter Tuning
- Key Concepts Glossary
- How to Run
- Output Files

---

## 1. Project Overview

**Goal:** Solar panel ki image dekh kar uski category predict karna (jaise Clean / Dusty / Damaged — jo bhi aapke dataset ki classes hain).

**Approach:** Notebook mein 5 models try kiye gaye hain, simple se complex:

```
Model 1: Basic CNN
   ↓
Model 2: + Dropout, BatchNorm (overfitting fix)
   ↓
Model 3: + Data Augmentation, L2 Regularization (advanced custom CNN)
   ↓
Model 4: MobileNetV2 (pre-trained model use kiya)
   ↓
Model 5: EfficientNetB0 + Hyperparameter Tuning (sabse advanced)
```

| | |
|---|---|
| **Input** | Solar panel images |
| **Output** | Class prediction (e.g. Clean / Dusty / Damaged) |
| **Image size** | 224 x 224 pixels |
| **Framework** | TensorFlow / Keras |

---

## 2. Dataset Structure

Code yeh expect karta hai:

```
solar-panel-classification/
└── Data/
    ├── Clean/
    │   ├── img1.jpg
    │   └── ...
    ├── Dusty/
    │   └── ...
    └── Damaged/
        └── ...
```

> 🔑 **Rule:** Folder ka naam hi class ka naam ban jata hai. `image_dataset_from_directory()` automatically yeh sub-folders detect kar leta hai.

---

## 3. Setup & Data Loading

| Cell | Kya karta hai |
|---|---|
| Imports (`tensorflow`, `matplotlib`, `os`) | Required libraries load karta hai |
| Drive mount | Colab ko Google Drive se connect karta hai *(local mein zaroorat nahi)* |
| Config variables | `IMG_HEIGHT/WIDTH = 224`, `BATCH_SIZE = 32`, `SEED = 42` set karta hai |
| `image_dataset_from_directory()` x2 | Data ko **80% training + 20% validation** mein split karta hai |
| `class_names`, `num_classes` | Folder names se class labels nikalta hai |

**Quick definitions:**
- 🟢 **Training data** → jisse model seekhta hai
- 🔵 **Validation data** → jisse model ka performance *check* hota hai (yeh seekhta nahi)
- 🎯 **Batch size** → ek baar mein kitni images model ko dikhayi jaayengi
- 🔁 **Seed** → same random split har baar milta hai (reproducibility)

---

## 4. Model 1: Basic CNN

### Architecture
```
Input Image (224x224x3)
   ↓ Rescaling (0-255 → 0-1)
   ↓ Conv2D(32) → MaxPooling
   ↓ Conv2D(64) → MaxPooling
   ↓ Conv2D(128) → MaxPooling
   ↓ Flatten
   ↓ Dense(128)
   ↓ Dense(num_classes) → Output (softmax)
```

### Layer-wise Explanation

| Layer | Kaam |
|---|---|
| `Rescaling(1/255)` | Pixel values 0-1 range mein normalize karta hai |
| `Conv2D` | Patterns (edges, textures) detect karta hai — number (32→64→128) jitna zyada, utne zyada filters |
| `MaxPooling2D` | Image size kam karta hai, important features rakhta hai |
| `Flatten` | 2D data ko 1D line mein convert karta hai |
| `Dense(128)` | Features combine kar decision leta hai |
| `Dense(num_classes, softmax)` | Final prediction — har class ki probability |

### Compile Settings
```python
optimizer = "adam"          # weights update karne ka algorithm
loss = "sparse_categorical_crossentropy"   # multi-class loss
metrics = ["accuracy"]
```

### Training
Pehle **10 epochs**, fir **20 epochs** tak train kiya gaya. Graph se accuracy trend dekha gaya.

> ⚠️ **Problem dekha gaya:** Training accuracy badhti gayi, par validation accuracy stuck ho gayi → yeh **overfitting** ka sign hai.

---

## 5. Model 2: Overfitting Fix

### Naye Tools Add Kiye Gaye

| Tool | Kaam |
|---|---|
| `EarlyStopping(patience=3)` | 3 epochs tak improvement na ho to training rok deta hai |
| `LearningRateScheduler` | 5 epochs ke baad learning rate 10x kam kar deta hai |
| `BatchNormalization()` | Training stable/fast banata hai |
| `Dropout(0.3)` / `Dropout(0.5)` | Randomly 30-50% neurons "off" kar overfitting kam karta hai |

### Updated Architecture
```
Conv2D(32) → BatchNorm → MaxPool → Dropout(0.3)
Conv2D(64) → BatchNorm → MaxPool → Dropout(0.3)
Conv2D(128) → BatchNorm → MaxPool → Dropout(0.3)
Flatten → Dropout(0.5) → Dense(128) → Output
```

> ⚠️ **Note:** `early_stopping` aur `lr_schedule` define hue, par `model.fit()` mein `callbacks=` parameter mein pass nahi kiye gaye is version mein — chahe to aage add kar sakte hain.

---

## 6. Model 3: Advanced CNN + Augmentation

> 💡 Yeh sabse strong **custom (non-pretrained)** model hai is notebook mein.

### Data Augmentation
```python
RandomFlip("horizontal")
RandomRotation(0.15)
RandomZoom(0.15)
```
Har epoch mein training images ko randomly flip/rotate/zoom kiya jata hai — model "naye" variations dekhta hai bina extra data ke. **Overfitting kam karne ka sabse powerful tarika.**

### Architecture Highlights
- Har Conv block mein **2 Conv2D layers** (pehle sirf 1 thi) → deeper feature learning
- `kernel_regularizer=l2(0.001)` → Dense layer weights ko bahut bada hone se rokta hai
- `ReduceLROnPlateau` → stuck hone par learning rate aadha kar deta hai
- `restore_best_weights=True` → training ke best epoch ke weights restore hote hain (last epoch ke nahi)

### Training Setup
| Setting | Value |
|---|---|
| Max epochs | 50 |
| Early stopping patience | 7 |
| Initial learning rate | 0.0003 |

---

## 7. Model 4: MobileNetV2 (Transfer Learning)

### 💡 Transfer Learning kya hai?
Naya model from-scratch train karne ke jagah, ek **already-trained model** (lakhon images pe trained) use karte hain — fast training, achhe results kam data mein bhi.

### Steps
```python
base_model = MobileNetV2(weights='imagenet', include_top=False)
base_model.trainable = False
```

| Setting | Matlab |
|---|---|
| `weights='imagenet'` | Pre-trained knowledge load karta hai |
| `include_top=False` | Original classifier hata diya (apna lagayenge) |
| `trainable = False` | Pre-trained layers **freeze** — training fast hoti hai |

### Naya Classifier Add Kiya
```
MobileNetV2 (frozen) → GlobalAveragePooling2D → Dense(128) → Output
```

- **`GlobalAveragePooling2D`** = Flatten ka lightweight alternative

### Result
- 20 epochs train, accuracy graph plot
- Model save: `mobilenetv2_2.h5`

---

## 8. Model 5: EfficientNetB0 (Transfer Learning)

Same Transfer Learning approach, **EfficientNetB0** ke saath + extra improvements:

### A) Class Imbalance Fix
```python
class_weights[index] = total_images / (num_classes * class_counts[class_name])
```
Agar kisi class ki images kam hain, to usse training mein **extra importance** di jati hai, taaki model use ignore na kare.

### B) Manual Preprocessing
```python
images = preprocess_input(images)   # EfficientNet-specific normalization
```
EfficientNet ko apna specific tarah ka pixel normalization chahiye hota hai — yeh function woh apply karta hai.

> ⚠️ **Note:** Preprocessed images (`train_images`) actually `model.fit()` mein use nahi hue is version mein — original `train_dataset` hi use hua. Yeh ek chhoti inconsistency hai.

### Architecture
```
EfficientNetB0 (frozen) → GlobalAveragePooling2D → Dense(128) → Output
```
15 epochs train kiya gaya.

---

## 9. Hyperparameter Tuning

### 💡 Kya hai yeh?
Model ki "settings" manually try karne ke jagah, tool automatically best combination dhundta hai.

### Kaunse Hyperparameters Tune Hue
| Parameter | Range |
|---|---|
| Rotation factor | 0.05 – 0.3 |
| Zoom factor | 0.05 – 0.3 |
| Dropout rate | 0.0 – 0.5 |
| Dense units | 64 – 512 |
| Learning rate | 0.0001 – 0.01 |

```python
tuner = kt.RandomSearch(build_model, objective='val_accuracy', max_trials=20)
tuner.search(train_dataset, validation_data=validation_dataset, epochs=20, class_weight=class_weights)
```

- **20 random combinations** try ki jaati hain
- Jo combination sabse best `val_accuracy` de, woh **best model** ban jata hai
- Best model save hota hai: `trained_effnet_finetune.h5`

> ⏱️ **Sabse zyada time lene wala step hai yeh** (20 trials × 20 epochs) — GPU pe hi chalayein.

---

## 10. Key Concepts Glossary

| Concept | Simple Explanation |
|---|---|
| **Conv2D** | Image patterns detect karta hai |
| **MaxPooling2D** | Image size chhota karta hai, info retain karke |
| **Dropout** | Randomly neurons band karta hai → overfitting kam |
| **BatchNormalization** | Training fast/stable banata hai |
| **Data Augmentation** | Images ko randomly modify karke variety badhata hai |
| **Transfer Learning** | Pre-trained model ka gyaan reuse karna |
| **EarlyStopping** | Overfitting se pehle training auto-rok deta hai |
| **ReduceLROnPlateau** | Stuck hone par learning rate kam karta hai |
| **Class Weights** | Kam images wali class ko extra importance |
| **Hyperparameter Tuning** | Best settings automatically dhundna |

---

## 11. How to Run

**Recommended path (fast + reliable):**
```
Step 1 → Setup + Data Loading   (zaroori)
Step 2 → Model 4: MobileNetV2   (best balance of speed + accuracy)
```

**Full exploration path (sab models try karne ke liye):**
```
Setup → Model 1 → Model 2 → Model 3 → Model 4 → Model 5 → Tuning
```

> 💡 Hyperparameter Tuning (Section 9) sabse last mein chalayein — sabse zyada time leta hai.

---

## 12. Output Files

| File Name | Source | Description |
|---|---|---|
| `mobilenetv2_2.h5` | Model 4 | Trained MobileNetV2 model |
| `trained_effnet_finetune.h5` | Model 5 (tuned) | Best EfficientNetB0 model |

**Load karne ka tarika:**
```python
loaded_model = tf.keras.models.load_model("mobilenetv2_2.h5")
```
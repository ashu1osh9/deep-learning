# 🔆 Solar Panel Classification — Flow & Concepts (Diagram-wise)

> Yeh document sirf **flow** dikhata hai — pehle kya kiya, uske baad kya kiya, aur har step pe **kaunsa concept/tool use hua**.

---

## 🗺️ Master Flow — Sabse Pehle Yeh Dekhein

```
┌──────────────────────────────────────┐
│ STEP 1: Setup & Data Loading         │
└───────────────────┬───────────────────┘
                    │
┌───────────────────▼───────────────────┐
│ STEP 2: Model 1 — Basic CNN           │
└───────────────────┬───────────────────┘
                    │
┌───────────────────▼───────────────────┐
│ STEP 3: Model 2 — Overfitting Fix     │
└───────────────────┬───────────────────┘
                    │
┌───────────────────▼───────────────────┐
│ STEP 4: Model 3 — Advanced CNN        │
└───────────────────┬───────────────────┘
                    │
┌───────────────────▼───────────────────┐
│ STEP 5: Model 4 — MobileNetV2         │
└───────────────────┬───────────────────┘
                    │
┌───────────────────▼───────────────────┐
│ STEP 6: Model 5 — EfficientNetB0      │
└───────────────────┬───────────────────┘
                    │
┌───────────────────▼───────────────────┐
│ STEP 7: Hyperparameter Tuning         │
└────────────────────────────────────────┘
```

Ab har step ko alag-alag dekhte hain — diagram + concepts jo use hue.

---

## STEP 1 — Setup & Data Loading

```
Drive mount → Config (224×224, batch=32) → image_dataset_from_directory()
       → 80% Training data + 20% Validation data → class_names nikale
```

**Concepts use hue:**
- `image_dataset_from_directory` — folder se images load karna
- `validation_split` — data ko train/validation mein automatically baantna
- Class names = folder names (automatic labeling)

---

## STEP 2 — Model 1: Basic CNN (Sabse Pehla Model)

```
Input image (224×224×3)
        │
        ▼
Rescaling (0–255 → 0–1)
        │
        ▼
Conv2D(32) → MaxPool
        │
        ▼
Conv2D(64) → MaxPool
        │
        ▼
Conv2D(128) → MaxPool
        │
        ▼
Flatten → Dense(128) → Output (softmax)
```

**Concepts use hue:**
- `Conv2D` — image patterns detect karna
- `MaxPooling2D` — image size kam karna
- `Flatten` + `Dense` — final decision lena
- `softmax` — class probabilities dena

**Result:** Training accuracy badhi, par validation accuracy stuck ho gayi → **overfitting** dekha gaya. *(Isi wajah se Step 3 kiya gaya.)*

---

## STEP 3 — Model 2: Overfitting Fix (Pehle Model Ki Problem Solve Ki)

```
Same CNN structure, lekin har Conv block ke baad:
Conv2D → BatchNorm → MaxPool → Dropout(0.3)
   (×3 blocks)
        │
        ▼
Flatten → Dropout(0.5) → Dense(128) → Output
```

**Naye concepts add hue (Model 1 se aage):**
- `EarlyStopping` — improvement na ho to training rok dena
- `LearningRateScheduler` — learning rate ko gradually kam karna
- `BatchNormalization` — training stable banana
- `Dropout` — randomly neurons band kar overfitting kam karna

---

## STEP 4 — Model 3: Advanced CNN (Aur Behtar Banaya)

```
Data Augmentation (Flip, Rotate, Zoom)
        │
        ▼
2× Conv2D per block (32→32, 64→64, 128→128)
   each with BatchNorm + Dropout
        │
        ▼
Dense(256, L2 reg) → Dense(128, L2 reg) → Output
```

**Naye concepts add hue (Step 3 se aage):**
- `RandomFlip / RandomRotation / RandomZoom` — Data Augmentation (training images ko randomly badalna)
- `l2 regularizer` — weights ko bahut bada hone se rokna
- `ReduceLROnPlateau` — stuck hone par learning rate aadha karna
- `restore_best_weights` — best epoch ke weights wapas lana

---

## STEP 5 — Model 4: MobileNetV2 (Pehli Baar Transfer Learning)

```
Image
  │
  ▼
┌─────────────────────────────┐
│ MobileNetV2 (FROZEN)        │  ← pre-trained on ImageNet
│ trainable = False           │
└──────────────┬──────────────┘
               │ features
               ▼
┌─────────────────────────────┐
│ GlobalAveragePooling2D      │
│ Dense(128) → Output         │  ← yeh hi sirf train hota hai
└─────────────────────────────┘
```

**Naya concept yahan introduce hua:**
- **Transfer Learning** — pehle se trained model (`weights='imagenet'`) use karna
- `base_model.trainable = False` — pre-trained layers ko freeze karna
- `GlobalAveragePooling2D` — Flatten ka lightweight alternative

**Output:** Model save hua → `mobilenetv2_2.h5`

---

## STEP 6 — Model 5: EfficientNetB0 (Transfer Learning + Extra Steps)

```
Same Transfer Learning pattern (jaisa Step 5 mein),
EfficientNetB0 base ke saath, + 2 naye additions:

[A] Class Imbalance Fix          [B] Manual Preprocessing
    class_weights{} calculate        preprocess_input()
    (kam images wali class ko            (EfficientNet-specific
     extra importance)                    normalization)
              │                              │
              └──────────────┬───────────────┘
                              ▼
                 EfficientNetB0 (frozen)
                              │
                              ▼
              GlobalAveragePooling2D → Dense(128) → Output
```

**Naye concepts add hue (Step 5 se aage):**
- `class_weight` — imbalanced classes ko handle karna
- `preprocess_input` — model-specific image normalization

**Output:** Best model ke liye aage Step 7 (tuning) kiya gaya.

---

## STEP 7 — Hyperparameter Tuning (Sabse Last Step)

```
build_model(hp) function banaya
        │
        ▼
kt.RandomSearch(max_trials=20)
        │
        ▼
20 different combinations try kiye:
  rotation, zoom, dropout, dense_units, learning_rate
        │
        ▼
Best combination wala model select hua
        │
        ▼
best_model.save('trained_effnet_finetune.h5')
```

**Naya concept yahan introduce hua:**
- **Keras Tuner (`RandomSearch`)** — best hyperparameters automatically dhundna, manually try karne ke jagah

---

## 📌 Concepts Summary — Kab Pehli Baar Use Hua

| Order | Concept | Pehli baar kahan use hua |
|---|---|---|
| 1 | Conv2D, MaxPooling, Flatten, Dense | Step 2 (Model 1) |
| 2 | EarlyStopping, BatchNorm, Dropout | Step 3 (Model 2) |
| 3 | Data Augmentation, L2 Regularization, ReduceLROnPlateau | Step 4 (Model 3) |
| 4 | Transfer Learning, Frozen base, GlobalAveragePooling | Step 5 (Model 4) |
| 5 | Class Weights, preprocess_input | Step 6 (Model 5) |
| 6 | Hyperparameter Tuning (Keras Tuner) | Step 7 |

> 💡 **Pattern dikh raha hai:** Har step pe pichhle step ki **kamzori fix** ki gayi hai — Model 1 mein overfitting thi → Model 2 mein fix ki → Model 3 mein aur strong banaya → fir CNN se hat ke Transfer Learning (Model 4, 5) try kiya for better accuracy → aakhir mein Tuning se best settings dhundi.
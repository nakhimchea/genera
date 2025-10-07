# [Audio] ASR Model Enhancement - Training - Data Augmentation

Apply these augmentation before training:
1. Speed
2. Noise
3. Volume
4. Pitch

SpecAugment is applied during nnet training (already in source code)

Modify code and test on personal laptop before performing official augmentation on desktop (made sure it also save original file and text in addition to the augmented to a new text and train directory).

Added checkpoint savings to the augmentation code

Setup rubberband-cli on desktop server because it doesn't exist yet (need it to run augmentation)

Augment on full training data x10

After augmentation, total training data is 876K (original + augmented) with 170GB file size and about 1500 hours.

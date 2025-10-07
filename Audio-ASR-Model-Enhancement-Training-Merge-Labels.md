# [Audio] ASR Model Enhancement - Training - Merge Labels

Upload text and splitted audio dataset from personal laptop to desktop
Merge text and splitted audio dataset from personal laptop with the existing one in desktop
extract audio folder name with no label (all folder have matching prefix in text)
extract audio file with no corresponding label from dataset and move it to "no_label" folder

Total text: 116K rows
Total audio folder is 236 (before moving audio file with no label):
* Train: 188 folders (75,709 files)
* Dev: 26 folders (18,508 files)
* Test: 22 folders (23,528 files)
After moving audio file with no label to "no_label" folder (moved 986 total file)
* Train: 188 folders (74,959 files)
* Dev: 26 folders (18,328 files)
* Test: 22 folders (22,855 files)

Found 2 audio folder (khounalexa and thai12345) that exist in both train and test folder. Moved it to test folder.
since training data is not 80%, need to move data from dev and test (but only the new batch, no touching the old data on desktop since b Nakhim already prepare it like that)

So the FINAL is:
* Train: 197 folders (79,653 files, 15.4GB, about 142 hours)
* Dev: 21 folders (15,664 files, 3.36GB, about 31 hours)
* Test: 16 folders (20,825 files, 5.50GB, about 51 hours)

![Dataset Properties](image-reference)

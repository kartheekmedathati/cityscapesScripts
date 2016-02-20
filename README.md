# The Cityscapes Dataset

This repository contains scripts for inspection, preparation, and evaluation of the Cityscapes dataset. This large-scale dataset contains a diverse set of stereo video sequences recorded in street scenes from 50 different cities, with high quality pixel-level annotations of 5 000 frames in addition to a larger set of 20 000 weakly annotated frames.

Details and download are available at: www.cityscapes-dataset.net


## Dataset Structure

The folder structure of the Cityscapes dataset is as follows:
```
{root}/{type}{video}/{split}/{city}/{city}_{seq:0>6}_{frame:0>6}_{type}{ext}
```

The meaning of the individual elements is:
 - `root`  the root folder of the Cityscapes dataset. Many of our scripts check if an environment variable `CITYSCAPES_DATASET` pointing to this folder exists and use this as the default choice.
 - `type`  the type/modality of data, e.g. `gtFine` for fine ground truth, or `leftImg8bit` for left 8-bit images.
 - `split` the split, i.e. train/val/test/train_extra. Note that not all kinds of data exist for all splits. Thus, do not be surprised to occasionally find empty folders.
 - `city`  the city in which this part of the dataset was recorded.
 - `seq`   the sequence number using 6 digits.
 - `frame` the frame number using 6 digits. Note that in some cities very few, albeit very long sequences were recorded, while in some cities many short sequences were recorded, of which only the 19th frame is annotated.
 - `ext`   the extension of the file and optionally a suffix, e.g. `_polygons.json` for ground truth files

Possible values of `type`
 - `gtFine`       the fine annotations, 2975 training, 500 validation, and 1525 testing. This type of annotations is used for validation, testing, and optionally for training. Annotations are encoded using `json` files containing the individual polygons. Additionally, we provide `png` images, where pixel values encode labels. Please refer to `helpers/labels.py` and the scripts in `preparation` for details.
 - `gtCoarse`     the coarse annotations, available for all training and validation images and for another set of 19998 training images (`train_extra`). These annotations can be used for training, either together with gtFine or alone in a weakly supervised setup.
 - `leftImg8bit`  the left images in 8-bit LDR format. These are the standard annotated images.
 - `leftImg16bit` the left images in 16-bit HDR format. These images offer 16 bits per pixel of color depth and contain more information, especially in very dark or bright parts of the scene. Warning: The images are stored as 16-bit pngs, which is non-standard and not supported by all libraries.
 - `rightImg8bit`  the right stereo views in 8-bit LDR format.
 - `rightImg16bit` the right stereo views in 16-bit HDR format.
 - `timestamp`     the time of recording in ns. The first frame of each sequence always has a timestamp of 0.
 - `disparity`     precomputed disparity depth maps. To obtain the disparity values, compute for each pixel p with p > 0: d = ( float(p) - 1. ) / 256., while a value p = 0 is an invalid measurement. Warning: the images are stored as 16-bit pngs, which is non-standard and not supported by all libraries.
 - `camera`        internal and external camera calibration.

More types might be added over time and also not all types are initially available. Please let us know if you need any other meta-data to run your approach.

Possible values of `split`
 - `train`       usually used for training, contains 2975 images with fine and coarse annotations
 - `val`         should be used for validation of hyper-parameters, contains 500 image with fine and coarse annotations. Can also be used for training.
 - `test`        used for testing on our evaluation server. The annotations are not public, but we include annotations of ego-vehicle and rectification border for convenience.
 - `train_extra` can be optionally used for training, contains 19998 images with coarse annotations



## Scripts

There are several scripts included with the dataset in a folder named `scripts`
 - `helpers`      helper files that are included by other scripts
 - `viewer`       view the images and the annotations
 - `preparation`  convert the ground truth annotations into a format suitable for your approach
 - `evaluation`   validate your approach


Note that all files have a small documentation at the top. Most important files
 - `helpers/labels.py`                               central file defining the IDs of all semantic classes and providing mapping between various class properties.
 - `viewer/cityscapesViewer.py`                      view the images and overlay the annotations.
 - `preparation/createTrainIdLabelImgs.py`           convert annotations in polygonal format to png images with label IDs, where pixels encode "train IDs" that you can define in `labels.py`.
 - `preparation/createTrainIdInstanceImgs.py`        convert annotations in polygonal format to png images with instance IDs, where pixels encode instance IDs composed of "train IDs".
 - `evaluation/evalPixelLevelSemanticLabeling.py`    script to evaluate pixel-level semantic labeling results on the validation set. This script is also used to evaluate the results on the test set.
 - `evaluation/setup.py`                             run `setup.py build_ext --inplace` to enable cython plugin for faster evaluation. Only tested for Ubuntu.




## Evaluation

Once you want to test your method on the test set, please run your approach on the provided test images and submit your results:
https://www.cityscapes-dataset.com/submit/
For semantic labeling, we require the result format to match the format of our label images named `labelIDs`.
Thus, your code should produce images where each pixel's value corresponds to a class ID as defined in `labels.py`.
Note that our evaluation scripts are included in the scripts folder and can be used to test your approach on the validation set.
For further details regarding the submission process, please consult our website.

## Contact

Please feel free to contact us with any questions, suggestions or comments:

* Marius Cordts, Mohamed Omran
* mail@cityscapes-dataset.net
* www.cityscapes-dataset.net
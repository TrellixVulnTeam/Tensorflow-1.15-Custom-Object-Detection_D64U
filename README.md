# ✅ Tensorflow-1.15-Custom-Object-Detection📸
This readme describes every step required to get going with your own object detection classifier:

Setting up the Object Detection directory structure
Gathering and labeling pictures
Generating training data
Creating a label map and configuring training
Training
Exporting the inference graph
Testing and using your newly trained object detection classifier
Appendix: Common Errors
# 👨🏻‍💻 Prerequisities: 
-Tensorflow 1.15.0, opencv-python==4.1.1.26,LabelImg

This example requires you to install Tensorflow 1.15 for training, opencv-python==4.1.1.26, and LabelImg for annotating your images

Just type in your terminal:

`pip install tensorflow==1.15`

# Installation procedures

Before starting doing custom object detection let's install and set up coding environment. For that make sure you download this repo to your local PC and inside `/research/` directory run following commands: 
```
python setup.py build
python setup.py install
```

These commands should install Tensorflow Object Detection API to your PC

# 📸 Data Collection & Annotation:
I used my smartphone to take photos of vegetables and used the LabelIMG annotation tool to annotate them. My dataset is made up of more than 6000 images of three classes - carrots, cucumbers and bananas. So for every class I have collected around 2000 images. The annotation part was the most time-consuming part of the project. I have made some research on existing online AI-powered annotation tools and here is the list of them: 



You can use any image dataset collection methods you want such as [Google Open Images](https://storage.googleapis.com/openimages/web/index.html), web scraping, taking photos by yourself and etc. The main goal here is to collect as many images as you can. But for this kind of project 400-600 images would also be enough. After you finished collecting images, now it is time to start annotating them. For this task I used LabelImg. LabelImg is very easy in use an can be used to make annotate in PASCAL VOC and YOLO formats. Make sure you watch some YouTube tutorials about LabelImg in order to know how to use this tool.

Full Documentation for this tool can be found [here](https://github.com/tzutalin/labelImg)


## TFRECORDS 
With the images labeled, it’s time to generate the TFRecords that serve as input data to the TensorFlow training model. This tutorial uses the xml_to_csv.py and generate_tfrecord.py scripts from Dat Tran’s Raccoon Detector dataset, with some slight modifications to work with our directory structure.

First, the image .xml data will be used to create .csv files containing all the data for the train and test images. From the \object_detection folder, issue the following command in the Anaconda command prompt:
  
```
python xml_to_csv.py
```
This creates a train_labels.csv and test_labels.csv file in the \object_detection\images folder.

Next, open the generate_tfrecord.py file in a text editor. Replace the label map starting at line 31 with your own label map, where each object is assigned an ID number. This same number assignment will be used when configuring the labelmap.pbtxt file in Step 5b.

For example, say you are training a classifier to detect basketballs, shirts, and shoes. You will replace the following code in generate_tfrecord.py:
```
# TO-DO replace this with label map
def class_text_to_int(row_label):
    if row_label == 'Carrot':
        return 1
    elif row_label == 'Cucumber':
        return 2
    elif row_label == 'Zucchini':
        return 3
    else:
        None
```

Then, generate the TFRecord files by issuing these commands from the \object_detection folder:

```
python generate_tfrecord.py --csv_input=images\train_labels.csv --image_dir=images\train --output_path=train.record
python generate_tfrecord.py --csv_input=images\test_labels.csv --image_dir=images\test --output_path=test.record
```


## Background/Negative Images.

If you are a newbie to object detection then you might be encountering the topic of usage of background/negative images for the first time. Do not worry, I described this phenomenon in this [article](https://medium.datadriveninvestor.com/what-is-not-taught-in-object-detection-tutorials-and-books-dc33ab4c3063). 
  

# Configuring the config files before training.
The last thing to do before training is to create a label map and edit the training configuration file.
Label map
The label map tells the trainer what each object is by defining a mapping of class names to class ID numbers. Use a text editor to create a new file and save it as labelmap.pbtxt in the C:\tensorflow1\models\research\object_detection\training folder. (Make sure the file type is .pbtxt, not .txt !) In the text editor, copy or type in the label map in the format below (the example below is the label map for my Pinochle Deck Card Detector):
```
item {
  id: 1
  name: 'Carrot'
}

item {
  id: 2
  name: 'Cucumber'
}

item {
  id: 3
  name: 'Zucchini'
}

```

Configure training
Finally, the object detection training pipeline must be configured. It defines which model and what parameters will be used for training. This is the last step before running training!

Navigate to models\research\object_detection\samples\configs and copy the faster_rcnn_inception_v2_pets.config file into the \object_detection\training directory. Then, open the file with a text editor. There are several changes to make to the .config file, mainly changing the number of classes and examples, and adding the file paths to the training data.

Make the following changes to the faster_rcnn_inception_v2_pets.config file. Note: The paths must be entered with single forward slashes (NOT backslashes), or TensorFlow will give a file path error when trying to train the model! Also, the paths must be in double quotation marks ( " ), not single quotation marks ( ' ).

Line 9. Change num_classes to the number of different objects you want the classifier to detect. For the above basketball, shirt, and shoe detector, it would be num_classes : 3 .

Line 106. Change fine_tune_checkpoint to:

fine_tune_checkpoint : "models/research/object_detection/faster_rcnn_inception_v2_coco_2018_01_28/model.ckpt"
Lines 123 and 125. In the train_input_reader section, change input_path and label_map_path to:

input_path : "models/research/object_detection/train.record"
label_map_path: "models/research/object_detection/training/labelmap.pbtxt"
Line 130. Change num_examples to the number of images you have in the \images\test directory.

Lines 135 and 137. In the eval_input_reader section, change input_path and label_map_path to:

input_path : "models/research/object_detection/test.record"
label_map_path: "models/research/object_detection/training/labelmap.pbtxt"
Save the file after the changes have been made. That’s it! The training job is all configured and ready to go!

# 💪 Training Model



# 🆒 Exporting trained model






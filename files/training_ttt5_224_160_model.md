***

# Tiny-tiny-tiny-yolo model(ttt5_224_160.cfg) training method  

**[pjreddie](https://pjreddie.com/darknet) or [AlexeyAB](https://github.com/AlexeyAB/darknet) recommends ensamble training for YOLO**.  
For ensamble training, we need **2-dataset imagenet and COCO**.  
And *classification task* by imagenet dataset and *object detection task* by COCO dataset.  

### Requirement
BASH   
PYTHON  
OPENCV-PYTHON  
WGET COMMAND  
CUDNN/GPU  

## Classification task by imagenet

### Preparation dataset
First of all, to get imagenet dataset, we need registration and downloading permission of WebMaster Stanford Univ. But not easy,,,  
Another way to get imagenet dataset is downloading URLs from imagenet Website and download many images using 'wget' command.  

**Git clone [AlexeyAB](https://github.com/AlexeyAB/darknet), and use scripts get_imagenet_train.sh in it.**  
these scripts perform downloading many jpeg images from internet, and finaly you get 3-files, trainval_1000c.txt, train_1000c.txt, val_1000c.txt.  
3-files mean jpeg image path-list on your PC for 1000 category training, validation and all.  
Images downloading from intenet takes a long time, **few days**.  

### Run classification training

After long time, image downloading is done.  
For training process, we have to git clone [ALexeyAB](https://github.com/AlexeyAB/darknet.git).  

$ git clone https://github.com/AlexeyAB/darknet.git
$ cd darknet

Next is to create darknet control card(cfg/*.data) like belllow,  
$ cat classifier_500c.data  
classes=500  
train  = <'path'>/get_imagenet/train_500c.txt  
valid  = <'path'>/get_imagenet/val_500c.txt  
labels = <'path'>/get_imagenet/darknet.labels_500c.txt  
backup = INET  
top = 5  

And issue training command,  
$ mkdir INET  
$ darknet classifier train classifier_500c.data cfg/ttt5_pre.cfg  
Classification training takes a long time too, **1week on tesla v100**.  
Classification training may not finished after 1week.  
After training, you can see weights in INET/ directory.

## Object detection task by COCO

### Preparation dataset

Other side, COCO dataset downloading is easy.  We just type 3 commands if not you use scripts in folder scripts.   
$ wget https://pjreddie.com/media/files/VOCtrainval_11-May-2012.tar  
$ wget https://pjreddie.com/media/files/VOCtrainval_06-Nov-2007.tar  
$ wget https://pjreddie.com/media/files/VOCtest_06-Nov-2007.tar  

### Run object detection training

Next is to create darknet control card(cfg/*.data) like bellow,  
$ cat cfg/coco.data  
classes= 80 
train  = <'path'>/train.txt  
valid  = <'path'>/2012.txt  
names = data/coco.names  
backup = COCO_WEIGHTS  

And issu training command,  
$ mkdir COCO_WEIGHTS  
$ darknet partial cfg/ttt5_pre.cfg INET/ttt5_pre.weights INET/ttt5_pre_7.weights 7  
$ darknet detector train cfg/coco.data cfg/ttt5_224_160.cfg INET/ttt5_pre_7.weights INET/ttt5_pre_7.weights  
COCO object detection trainig take **a day on tesla v100**.  
After training, you can see weights file in COCO_WEIGHTS/ directory.  

We get accuracy of training by issue command bellow,  
$ darknet detector recall cfg/coco.data cfg/ttt5_224_160.cfg  COCO_WEIGHTS/ttt5_224_160_final.weights  

.. DeepStack documentation master file, created by
   sphinx-quickstart on Wed Dec 12 17:30:35 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Object Detection
=================

The object detection API locates and classifies 80 different kinds of objects in a single image.

To use this API, you need to set **VISION-DETECTION=True** when starting DeepStack ::

    sudo docker run -e VISION-DETECTION=True -v localstorage:/datastore \
    -p 80:5000 deepquestai/deepstack

If using the GPU Version, run ::

    sudo docker run --rm --runtime=nvidia -e VISION-DETECTION=True -v localstorage:/datastore \
    -p 80:5000 deepquestai/deepstack:gpu

*Note also that you can have multiple endpoints activated, for example, both face and object detection are activated below* ::

    sudo docker run -e VISION-DETECTION=True  -e VISION-FACE=True -v localstorage:/datastore \
    -p 80:5000 deepquestai/deepstack



**Example**

.. figure:: test-image3.jpg
    :align: center

::

    const request = require("request")
    const fs = require("fs")

    image_stream = fs.createReadStream("test-image3.jpg")

    var form = {"image":image_stream}

    request.post({url:"http://localhost:80/v1/vision/detection", formData:form},function(err,res,body){

        response = JSON.parse(body)
        predictions = response["predictions"]
        for(var i =0; i < predictions.length; i++){

            console.log(predictions[i]["label"])

        }

        console.log(response)
    })

Result ::

    person
    person
    dog
    { success: true,
    predictions: 
    [ { confidence: 99,
       label: 'person',
       y_min: 89,
       x_min: 297,
       y_max: 513,
       x_max: 444 },
     { confidence: 99,
       label: 'person',
       y_min: 114,
       x_min: 443,
       y_max: 516,
       x_max: 598 },
     { confidence: 99,
       label: 'dog',
       y_min: 354,
       x_min: 640,
       y_max: 544,
       x_max: 810 } ] }

Using the object coordinates, we shall use the `Easy Image <https://www.npmjs.com/package/easyimage />`_ library to extract the faces and save them

::

    const request = require("request")
    const fs = require("fs")
    const easyimage = require("easyimage")

    image_stream = fs.createReadStream("test-image3.jpg")

    var form = {"image":image_stream}

    request.post({url:"http://localhost:80/v1/vision/detection", formData:form},function(err,res,body){

        response = JSON.parse(body)
        predictions = response["predictions"]
        for(var i =0; i < predictions.length; i++){
        
            pred = predictions[i]
            label = pred["label"]
            y_min = pred["y_min"]
            x_min = pred["x_min"]
            y_max = pred["y_max"]
            x_max = pred["x_max"]
       
            easyimage.crop(
                {
                src: "test-image3.jpg",
                dst: i.toString() + "_" + label+"_.jpg",
                x: x_min,
                cropwidth: x_max - x_min,
                y: y_min,
                cropheight: y_max - y_min,
            }
        )

        }

    })

Result

.. figure:: image0_dog.jpg
    :align: center

.. figure:: image1_person.jpg
    :align: center

.. figure:: image2_person.jpg
    :align: center


**CLASSES**

The following are the classes of objects DeepStack can detect in images ::

    person,   bicycle,   car,   motorcycle,   airplane,
    bus,   train,   truck,   boat,   traffic light,   fire hydrant,   stop_sign,
    parking meter,   bench,   bird,   cat,   dog,   horse,   sheep,   cow,   elephant,  
    bear,   zebra, giraffe,   backpack,   umbrella,   handbag,   tie,   suitcase,   
    frisbee,   skis,   snowboard, sports ball,   kite,   baseball bat,   baseball glove, 
    skateboard,   surfboard,   tennis racket, bottle,   wine glass,   cup,   fork,   
    knife,   spoon,   bowl,   banana,   apple,   sandwich,   orange, broccoli,   carrot,   
    hot dog,   pizza,   donot,   cake,   chair,   couch,   potted plant,   bed, dining table,   
    toilet,   tv,   laptop,   mouse,   remote,   keyboard,   cell phone,   microwave,
    oven,   toaster,   sink,   refrigerator,   book,   clock,   vase,   scissors,   teddy bear,
    hair dryer, toothbrush.

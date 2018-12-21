.. DeepStack documentation master file, created by
   sphinx-quickstart on Wed Dec 12 17:30:35 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Object Detection
================

The object detection api locates and classifies 80 different kinds of objects in a single image.

**Example**

.. figure:: test-image3.jpg
    :align: center

::

    const request = require("request")
    const fs = require("fs")

    image_stream = fs.createReadStream("family.jpg")

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
    { predictions: 
        [ { y_max: 530,
            y_min: 153,
            confidence: 99,
            x_min: 398,
            x_max: 705,
            label: 'person' },
          { y_max: 517,
            y_min: 117,
            confidence: 99,
            x_min: 625,
            x_max: 938,
            label: 'person' } ],
    success: true }


**DETECTION SPEED**

DeepStack offers three modes for object detection, the default mode is 
"LOW" offering good performance at maximum speed, the second mode is "MEDIUM"
while the third is "HIGH". The HIGH mode is much slower but offers the best 
performance. It is able to detect smaller details which the "LOW" and "MEDIUM" 
modes may failt to detect.

You can specify the mode in the run command for DeepStack has seen below ::

    sudo docker run -e MODE=HIGH -v localstorage:/datastore -p 80:5000 deepquestai/deepstack 

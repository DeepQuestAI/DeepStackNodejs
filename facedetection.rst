.. DeepStack documentation master file, created by
   sphinx-quickstart on Wed Dec 12 17:30:35 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Face Detection
==============

The face detection API detects faces and returns their coordinates.
It functions similarly to the face recognition API except that it does not 
perform recognition. 


**Example**

.. figure:: family.jpg
    :align: center

::

    const request = require("request")
    const fs = require("fs")

    image_stream = fs.createReadStream("family.jpg")

    var form = {"image":image_stream}

    request.post({url:"http://localhost:80/v1/vision/face", formData:form},function(err,res,body){

        response = JSON.parse(body)
        predictions = response["predictions"]
        
        console.log(response)
    })

Result ::

    { success: true,
        predictions:
        [ { confidence: 0.99990666,
            y_min: 145,
            x_min: 626,
            y_max: 261,
            x_max: 712 },
        { confidence: 0.99986553,
            y_min: 174,
            x_min: 543,
            y_max: 288,
            x_max: 620 },
        { confidence: 0.99986434,
            y_min: 163,
            x_min: 731,
            y_max: 242,
            x_max: 810 },
        { confidence: 0.99899536,
            y_min: 197,
            x_min: 477,
            y_max: 279,
            x_max: 542 } ] }

Using the face coordinates, we shall use the `Easy Image <https://www.npmjs.com/package/easyimage />`_ library to extract the faces and save them
::

    const request = require("request")
    const fs = require("fs")
    const easyimage = require("easyimage")

    image_stream = fs.createReadStream("family.jpg")

    var form = {"image":image_stream}

    request.post({url:"http://localhost:80/v1/vision/face", formData:form},function(err,res,body){

        response = JSON.parse(body)
        predictions = response["predictions"]
        for(var i =0; i < predictions.length; i++){
        
            pred = predictions[i]
            gender = pred["gender"]
            y_min = pred["y_min"]
            x_min = pred["x_min"]
            y_max = pred["y_max"]
            x_max = pred["x_max"]
       
            easyimage.crop(
                {
                src: "family.jpg",
                dst: i.toString() + "_.jpg",
                x: x_min,
                cropwidth: x_max - x_min,
                y: y_min,
                cropheight: y_max - y_min,
                }
            )

        }
    })

Result

.. figure:: image0_female.jpg
    :align: center

.. figure:: image1_male.jpg
    :align: center

.. figure:: image2_male.jpg
    :align: center

.. figure:: image3_female.jpg
    :align: center


**Performance**

DeepStack offers three modes allowing you to tradeoff speed for peformance. 
During startup, you can specify performance mode to be , **"High" , "Medium" and "Low"**

The default mode is "Medium"

You can speciy a different mode as seen below ::

    sudo docker run -e MODE=High -e VISION-FACE=True -v localstorage:/datastore \
    -p 80:5000 deepquestai/deepstack

Note the -**e MODE=High** above 


**Setting Minimum Confidence**

By default, the minimum confidence for detecting faces is 0.45. The confidence ranges between 0 and 1.
If the confidence level for a face falls below the min_confidence, no face is detected.

The min_confidence parameter allows you to increase or reduce the minimum confidence.

We lower the confidence allowed below.

Example ::

    const request = require("request")
    const fs = require("fs")

    image_stream = fs.createReadStream("family.jpg")

    var form = {"image":image_stream, "min_confidence":0.30}

    request.post({url:"http://localhost:80/v1/vision/face", formData:form},function(err,res,body){

        response = JSON.parse(body)
        predictions = response["predictions"]
        
        console.log(response)
    })

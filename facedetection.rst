.. DeepStack documentation master file, created by
   sphinx-quickstart on Wed Dec 12 17:30:35 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Face Detection
==============

The face detection API detects faces and returns their coordinates as well as the gender.
It functions similarly to the face recognition API except that it does not 
perform recognition. 
Also note that the recognition API does not return gender predictions.

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
        for(var i =0; i < predictions.length; i++){

            console.log(predictions[i]["gender"])

        }

        console.log(response)
    })

Result ::

    female
    male
    male
    female
    { success: true,
    predictions: 
    [ { confidence: 0.99999213,
       gender: 'female',
       y_min: 174,
       x_min: 534,
       y_max: 303,
       x_max: 629 },
     { confidence: 0.6611953,
       gender: 'male',
       y_min: 146,
       x_min: 616,
       y_max: 275,
       x_max: 711 },
     { confidence: 0.99884146,
       gender: 'male',
       y_min: 147,
       x_min: 729,
       y_max: 259,
       x_max: 811 },
     { confidence: 0.99997365,
       gender: 'female',
       y_min: 190,
       x_min: 471,
       y_max: 290,
       x_max: 549 } ] }

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
                dst: i.toString() + "_" + gender+"_.jpg",
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
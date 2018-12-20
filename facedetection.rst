.. DeepStack documentation master file, created by
   sphinx-quickstart on Wed Dec 12 17:30:35 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Face Detection!
==================

The face detection api detects faces and returns their coordinates as well as the gender.
It functions similarly to the face recognition api except that it does not 
perform recognition. 
Also note that the recognition api does not return gender predictions.

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
    { predictions: 
        [ { gender: 'female',
            y_max: 303,
            y_min: 174,
            confidence: 100,
            x_min: 534,
            x_max: 629 },
        { gender: 'male',
            y_max: 275,
            y_min: 146,
            confidence: 99,
            x_min: 616,
            x_max: 711 },
        { gender: 'male',
            y_max: 259,
            y_min: 147,
            confidence: 98,
            x_min: 729,
            x_max: 811 },
        { gender: 'female',
            y_max: 290,
            y_min: 190,
            confidence: 99,
            x_min: 471,
            x_max: 549 } ],
    success: true }

**THE RETURNED FACE COORDINATES CAN BE USED TO EXTRACT THE FACES**

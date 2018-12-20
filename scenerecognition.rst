.. DeepStack documentation master file, created by
   sphinx-quickstart on Wed Dec 12 17:30:35 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Scene Recognition
====================

The traffic recognition api classifies an image into one of 365 scenes


**Example**

.. figure:: test-image5.jpg
    :align: center

::

    const request = require("request")
    const fs = require("fs")

    image_stream = fs.createReadStream("test-image5.jpg")

    var form = {"image":image_stream}

    request.post({url:"http://localhost:80/v1/vision/scene", formData:form},function(err,res,body){

        response = JSON.parse(body)
        console.log(response)

    })

Result ::

    { predictions: [ { confidence: 73.73981475830078, label: 'conference_room' } ],
  success: true }

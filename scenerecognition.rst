.. DeepStack documentation master file, created by
   sphinx-quickstart on Wed Dec 12 17:30:35 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Scene Recognition
====================

The traffic recognition api classifies an image into one of 365 scenes


To use this API, you need to set **VISION-SCENE=True** when starting DeepStack ::

    sudo docker run -e VISION-SCENE=True -v localstorage:/datastore \
    -p 80:5000 deepquestai/deepstack

If using the GPU Version, run ::

    sudo docker run --rm --runtime=nvidia -e VISION-SCENE=True -v localstorage:/datastore \
    -p 80:5000 deepquestai/deepstack:gpu

*Note also that you can have multiple endpoints activated, for example, both traffic and scene recognition are activated below* ::

    sudo docker run -e VISION-SCENE=True  -e VISION-TRAFFIC=True -v localstorage:/datastore \
    -p 80:5000 deepquestai/deepstack


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

    { success: true, label: 'conference_room', confidence: 73.739815 }



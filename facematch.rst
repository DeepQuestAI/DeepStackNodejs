.. DeepStack documentation master file, created by
   sphinx-quickstart on Wed Dec 12 17:30:35 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

.. _facematch:

Face Match
===========

The face detection api compares faces in two different pictures and tells the similarity between them.
A typical use of this is matching identity documents with pictures of a person.


**Example**

Here we shall compare two pictures of obama

.. figure:: test-image6.jpeg
    :align: center

.. figure:: test-image7.jpg
    :align: center

::

    const request = require("request")
    const fs = require("fs")

    image_stream1 = fs.createReadStream("test-image6.jpeg")
    image_stream2 = fs.createReadStream("test-image7.jpg")
 
    var form = {"image1":image_stream1,"image2":image_stream2}

    request.post({url:"http://localhost:80/v1/vision/face/match", formData:form},function(err,res,body){

        response = JSON.parse(body)
        console.log(response)
    })

Result ::

    { success: true, similarity: 0.73975885 }

Here we shall compare a picture of Obama with that of Bradley Cooper

.. figure:: test-image6.jpeg
    :align: center

.. figure:: test-image8.jpg
    :align: center

::

    const request = require("request")
    const fs = require("fs")

    image_stream1 = fs.createReadStream("test-image6.jpeg")
    image_stream2 = fs.createReadStream("test-image8.jpg")
 
    var form = {"image1":image_stream1,"image2":image_stream2}

    request.post({url:"http://localhost:80/v1/vision/face/match", formData:form},function(err,res,body){

        response = JSON.parse(body)
        console.log(response)
    })


Result ::

    { success: true, similarity: 0.4456826 }


As seen above, the match for two different pictures of Obama was very high while the match for Obama and Bradley Cooper was very low.

**Performance**

DeepStack offers three modes allowing you to tradeoff speed for peformance. 
During startup, you can specify performance mode to be , **"High" , "Medium" and "Low"**

The default mode is "Medium"

You can speciy a different mode as seen below ::

    sudo docker run -e MODE=High -e VISION-FACE=True -v localstorage:/datastore \
    -p 80:5000 deepquestai/deepstack

Note the -**e MODE=High** above 
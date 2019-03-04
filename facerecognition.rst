.. DeepStack documentation master file, created by
   sphinx-quickstart on Wed Dec 12 17:30:35 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

.. _facerecognition:

Face Recognition
=================

In the Getting Started, we had an overview of the face recognition API. In this section, we shall explore all the functionalities 
of the API.

Face Registeration
------------------

The face registeration endpoint allows you to register pictures of person and associate it with a userid.

You can specify multiple pictures per person during registeration.

Example ::

    const request = require("request")
    const fs = require("fs")

    run_prediction("image1.jpg","User Name")

    function run_prediction(image_path,userid){

        image_stream = fs.createReadStream(image_path)

        var form = {"image":image_stream,"userid":userid}

        request.post({url:"http://localhost:80/v1/vision/face/register", formData:form},function(err,res,body){

            response = JSON.parse(body)
            console.log(response)

        })

    }

Result ::

    { success: true, message: 'face added' }

The response above indicates the call was successful. You should always check for the "success" status.
If their is an error in your request, you will receive a response like ::

    {error: 'user id not specified', success: False}

This indicates that you ommited the userid in your request.
If you ommited the image, the response will be ::

    {error: 'No valid image file found', success: False}



Face Recognition
-----------------
    
The face registeration endpoint detects all faces in an image and returns the USERID for each face. Note that the USERID was specified
during the registeration phase. If a new face is encountered, the USERID will be unknown. 

We shall test this on the image below.

.. figure:: test-image2.jpg
    :align: center
    
::
    
    const request = require("request")
    const fs = require("fs")

    image_stream = fs.createReadStream("test-image2.jpg")

    var form = {"image":image_stream}

    request.post({url:"http://localhost:80/v1/vision/face/recognize", formData:form},function(err,res,body){

        response = JSON.parse(body)
        predictions = response["predictions"]
        for(var i =0; i < predictions.length; i++){

            console.log(predictions[i]["userid"])

        }

        console.log(response)

    })

Result ::

    Idris Elba
    unknown
    { success: true,
    predictions:
        [ { confidence: 0.76965684,
            userid: 'Idris Elba',
            y_min: 154,
            x_min: 1615,
            y_max: 682,
            x_max: 1983 },
           { confidence: 0,
             userid: 'unknown',
            y_min: 237,
            x_min: 869,
            y_max: 732,
            x_max: 1214 } ] }


As you can see above, the first user is unknown since we did not previously register her, however, Idris Elba was detected as we
registered a picture of his in the previous tutorial.
Note also that the full response contains the coordinates of the faces.

Extracting Faces
----------------

The face coordinates allows you to easily extract the detected faces.
Here we shall use the `Easy Image <https://www.npmjs.com/package/easyimage />`_ library to extract the faces and save them ::

    const request = require("request")
    const fs = require("fs")
    const easyimage = require("easyimage")

    image_stream = fs.createReadStream("test-image2.jpg")

    var form = {"image":image_stream}

    request.post({url:"http://localhost:80/v1/vision/face/recognize", formData:form},function(err,res,body){

        response = JSON.parse(body)
        predictions = response["predictions"]
        for(var i =0; i < predictions.length; i++){
            pred = predictions[i]
            userid = pred["userid"]
            y_min = pred["y_min"]
            x_min = pred["x_min"]
            y_max = pred["y_max"]
            x_max = pred["x_max"]

            easyimage.crop(
                {
                    src: "test-image2.jpg",
                    dst: userid+".jpg",
                    x: x_min,
                    cropwidth: x_max - x_min,
                    y: y_min,
                    cropheight: y_max - y_min,
                }
            )
        }
    })

Result

    .. figure:: Idris-Elba.jpg
        :align: center

    .. figure:: unknown.jpg
        :align: center



**Setting Minimum Confidence**

DeepStack recognizes faces by computing the similarity between the embedding of a new face and the set of embeddings of previously registered faces.
By default, the minimum confidence is 0.67. The confidence ranges between 0 and 1.
If the similarity for a new face falls below the min_confidence, unknown will be returned.

The min_confidence parameter allows you to increase or reduce the minimum confidence.

We lower the confidence allowed below.

Example ::

    const request = require("request")
    const fs = require("fs")

    image_stream = fs.createReadStream("test-image2.jpg")

    var form = {"image":image_stream,"min_confidence":0.30}

    request.post({url:"http://localhost:80/v1/vision/face/recognize", formData:form},function(err,res,body){

        response = JSON.parse(body)
        predictions = response["predictions"]
        for(var i =0; i < predictions.length; i++){
            pred = predictions[i]
            console.log(pred["userid"])
        }
    })

Result ::

    Adele
    Idris Elba
    
By reducing the allowed confidence, the system detects the first face as Adele. The lower the confidence, the more likely
for the system to make mistakes. When the confidence level is high, mistakes are extremely rare, however, the system may 
return unknown always if the confidence is too high.

**For security related processes such as authentication, set the min_confidence at 0.7 or higher**

Managing Registered Faces
--------------------------

The face recognition API allows you to retrieve and delete faces
that has been previously registered with DeepStack.

Listing faces ::

    const request = require("request")

    request.post("http://localhost:80/v1/vision/face/list",function(err,res,body){

        response = JSON.parse(body)
        console.log(response)

    })

Result ::

    { success: true, faces: [ 'Adele', 'Christina Perri', 'Idris Elba', 'Tom Cruise' ] }



Deleting a face ::

    const request = require("request")

    var form = {"userid":"Idris Elba"}

    request.post({url:"http://localhost:80/v1/vision/face/delete", formData:form},function(err,res,body){

        response = JSON.parse(body)
        console.log(response)
    })

Result ::

    {success: True}

Having deleted Idris Elba from our database, we shall now attempt to recognize him
in our test image.


:: 

    const request = require("request")
    const fs = require("fs")

    image_stream = fs.createReadStream("test-image2.jpg")

    var form = {"image":image_stream}

    request.post({url:"http://localhost:80/v1/vision/face/recognize", formData:form},function(err,res,body){

        response = JSON.parse(body)
        predictions = response["predictions"]
        for(var i =0; i < predictions.length; i++){
            pred = predictions[i]
            console.log(pred["userid"])
        }
    })


Result ::

    unknown
    unknown
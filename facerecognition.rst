.. DeepStack documentation master file, created by
   sphinx-quickstart on Wed Dec 12 17:30:35 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Face Recognition
=================

In the Getting Started, we had an overview of the face recognition api. In this section, we shall explore all the functionalities 
of the api.

**Face Registeration** 

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

    {'predictions': {'message': 'face added'}, 'success': True}

The response above indicates the call was successful. You should always check for the "success" status.
If their is an error in your request, you will receive a response like ::

    {'error': 'user id not specified', 'success': False}

This indicates that you ommited the userid in your request.
If you ommited the image, the response will be ::

    {'error': 'No valid image file found', 'success': False}



**FACE RECOGNITION**
    
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

    })

Result  ::

    unknown
    Idris Elba
    Full Response:  {'predictions': [{'userid': 'unknown', 'y_max': 827, 'x_min': 850, 'y_min': 233, 'x_max': 1290}, {'x_min': 1577, 'confidence': 0.6002725154510684, 'userid': 'Idris Elba', 'y_max': 767, 'y_min': 160, 'x_max': 2041}], 'success': True}

As you can see above, the first user is unknown since we did not previously register her, however, Idris Elba was detected as we
registered a picture of his in the previous tutorial.
Note also that the full response contains the coordinates of the faces.

**You can use the face coordinates to extract the faces from the image.**


**Setting Face Distance**

DeepStack recognizes faces by computing the distance between a new face and set of embeddings of previously registered faces.
By default, if the distance is as low as 0.45, the user will be recognized, however, you can increase or decrease the distance
using the "min_distance" parameter.

Example ::

    const request = require("request")
    const fs = require("fs")

    image_stream = fs.createReadStream("test-image2.jpg")

    var form = {"image":image_stream,"min_distance":0.8}

    request.post({url:"http://localhost:80/v1/vision/face/recognize", formData:form},function(err,res,body){

        response = JSON.parse(body)
        predictions = response["predictions"]
        for(var i =0; i < predictions.length; i++){

            console.log(predictions[i]["userid"])

        }

    })

Result ::

    Christina Perri
    Idris Elba
    Full Response:  {'predictions': [{'y_min': 233, 'x_max': 1290, 'userid': 'Christina Perri', 'x_min': 850, 'y_max': 827, 'confidence': 0.2758451809953383}, {'y_min': 160, 'x_max': 2041, 'userid': 'Idris Elba', 'x_min': 1577, 'y_max': 767, 'confidence': 0.6002725154510684}], 'success': True}

By increasing the distance allowed, the system detects the first user as "Christina Perri".
The lower the distance, the more accurate the system is. However, if the distance is too low, the system may not detect any user.


**FACE LISTING**

The face list api allows you to list all registered faces ::

    const request = require("request")

    request.post("http://localhost:80/v1/vision/face/list",function(err,res,body){

        response = JSON.parse(body)
        console.log(response)

    })

Result ::

    {'success': True, 'faces': ['Tom Cruise', 'Adele', 'Idris Elba', 'Christina Perri']}

**DELETING FACE**

The face deletion api allows you to delete any of the registered faces ::

    const request = require("request")
    
    var form = {"userid":"Adele"}

    request.post({url:"http://localhost:80/v1/vision/face/delete", formData:form},function(err,res,body){

        response = JSON.parse(body)
        console.log(response)

    })

Result ::

    { message: 'user deleted successfully', success: true }



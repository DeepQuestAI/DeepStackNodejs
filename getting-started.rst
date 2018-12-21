.. DeepStack documentation master file, created by
   sphinx-quickstart on Wed Dec 12 17:30:35 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Getting Started with DeepStack
===============================

DeepStack is distributed as a docker image. In this tutorial, we shall 
go through the complete process of setting up docker and DeepStack and using it
to build a Face Recognition system.

**Setting Up Docker** 

Docker is a container platform that allows developers to distribute applications
as self contained packages that ships every dependency from the
operating system to the app dependences. It is similar to virtual machines
but is more lightweight and easier to manage. 

You can learn more about Docker on `Docker's Website <https://docker.io />`_
Visit  `Docker Getting Started <https://docs.docker.com/get-started />`_ for instructions on setting up and using Docker for the first time.

**Setting Up DeepStack**

DeepStack is an AI Server that provides AI features as APIs consumable via basic web requests.
It works entirely offline and can be installed anywhere docker runs both on premise and in the cloud.

Run the command below to install and start DeepStack ::
    
    docker run -v localstorage:/datastore -p 80:5000 deepquestai/deepstack

The part "-v localstorage:/datastore" specifies a permanent storage where deepstack will keep registered faces.

**Face Recognition**
    
    Think of a software that can identity known people by their names. Face Recognition does exactly that. Register a picture of a number of people
    and the system will be able to recognize them again anytime.
    Face Recognition is a two step process: The first is to register a known face and second is to recognize unknown faces.

**REGISTERING A FACE**
    
    Here we are building an application that can tell the names of a number of popular celebrities.
    First we collect pictures of a number of celebrities and we register them with deepstack


    .. figure:: cruise.jpg
        :align:  left

    .. figure:: adele.jpg
        :align:  left

    .. figure:: elba.jpg
        :align:  left

    .. figure:: perri.jpg
        :align:  left

Below we will register the faces with their names ::
    
    const request = require("request")
    const fs = require("fs")

    run_prediction("cruise.jpg","Tom Cruise")
    run_prediction("elba.jpg","Idris Elba")
    run_prediction("perri.jpg","Christina Perri")
    run_prediction("adele.jpg","Adele")

    function run_prediction(image_path,userid){

        image_stream = fs.createReadStream(image_path)

        var form = {"image":image_stream,"userid":userid}

        request.post({url:"http://localhost:80/v1/vision/face/register", formData:form},function(err,res,body){

            response = JSON.parse(body)
            console.log(response)

        })

    }

Result ::

    { predictions: { message: 'face added' }, success: true }
    { predictions: { message: 'face added' }, success: true }
    { predictions: { message: 'face added' }, success: true }
    { predictions: { message: 'face added' }, success: true }



**RECOGNITION**

Now we shall attempt to recognize any of these celebrities using DeepStack.
Below we will send in a whole new picture of Adele and DeepStack will attempt to
predict the name.

.. figure:: test-image.jpg
    :align:  left
    

Prediction code ::

    const request = require("request")
    const fs = require("fs")

    image_stream = fs.createReadStream("test-image.jpg")

    var form = {"image":image_stream}

    request.post({url:"http://localhost:80/v1/vision/face/recognize", formData:form},function(err,res,body){

        response = JSON.parse(body)
        predictions = response["predictions"]
        for(var i =0; i < predictions.length; i++){

            console.log(predictions[i]["userid"])

        }

    })

Result ::

    Adele
 
.. toctree::
   :maxdepth: 2
   :caption: Contents:

We have just created a face recognition system. You can try with different people and test on different pictures of them.

The next tutorial is dedicated to the full power of the face recognition api as well as best practices to make the best out of it.


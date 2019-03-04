Below, using DeepStack we attempt to classify the scene of the above image ::
    
    const request = require("request")
    const fs = require("fs")

    image_stream = fs.createReadStream("image.jpg")

    var form = {"image":image_stream}

    request.post({url:"http://localhost:80/v1/vision/scene", formData:form},function(err,res,body){

    response = JSON.parse(body)
    console.log(response)

    })

**Result**  ::

    { success: true, label: 'highway', confidence: 63.377846 }
# Amazon Bedrock Custom Model Import (CMI)

Bedrock Custom Model Import allows for importing foundation models that have been customized in other environments outside of Amazon Bedrock, such as Amazon Sagemaker, EC2, etc.

In our example we are assuming that a model was fine tuned prior. We will be using Qwen-3 model. Similar approach can work with any model.

## Importing Model to Amazon Bedrock

To import a model, model artifacts need to be uploaded in a S3 bucket. Before you upload your model weights into the bucket make sure that:
1. Uploaded weights are supported in the Amazon Bedrock Custom Model Import (CMI).

[This](https://docs.aws.amazon.com/bedrock/latest/userguide/model-customization-import-model.html) page lists updated documentation on the matter.

Once model artifacts are uploaded into an S3 bucket, we can import it into Amazon Bedrock.

In the AWS console, we can go to the Amazon Bedrock page. On the left side under "Foundation models" we will click on "Imported models"

![Step 1](./images/step1.png "Step 1")


You can now click on "Import model"

![Step 2](./images/step2.png "Step 2")

In this next step you will have to configure:

1. Model Name 
2. Import Job Name 
3. Model Import Settings 
    a. Select Amazon S3 bucket 
    b. Select your bucket location
4. You can optionally:
    a. Customize encryption. You can use your encryption key so that the models are encrypted at rest with your encryption key. 
    b. Create a service role that grants Bedrock permissions to access AWS services on your behalf. 
    c. Use a VPC Setting, so that your model is only accessible within your VPC configuration.
    d. Add to the job and the model.
5. Click Import Model (not shown in image)

![Step 3](./images/step3.png "Step 3")

You will now be taken to the page below. Your model may take up to an hour to import. 

![Step 4](./images/step4.png "Step 4")

After your model imports you will then be able to test it via the playground or API! 

![Playground](./images/playground.gif "Playground")

You can also use call the model programmatically. 

Below example shows a call with boto3:

```python

import json
import boto3
from botocore.config import Config

REGION_NAME = 'eu-central-1'
MODEL_ID= '' # You can use the model ARN

config = Config(
    retries={
        'total_max_attempts': 10,
        'mode': 'standard'
    }
)
message = "Hello, what is the date today?"

session = boto3.session.Session()
br_runtime = session.client(service_name = 'bedrock-runtime', 
                                 region_name=REGION_NAME, 
                                 config=config)
    
try:
    invoke_response = br_runtime.invoke_model(modelId=MODEL_ID, 
                                            body=json.dumps({'prompt': message}), 
                                            accept="application/json", 
                                            contentType="application/json")
    invoke_response["body"] = json.loads(invoke_response["body"].read().decode("utf-8"))
    print(json.dumps(invoke_response, indent=4))
except Exception as e:
    print(e)
    print(e.__repr__())
```


## Clean Up 

You can delete your Imported Model in the console as shown in the image below:

![Delete](./images/delete.png "Delete")

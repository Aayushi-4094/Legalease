# **Legalease - Legal Document Summarization**  

**Legalease** is a **machine learning-powered legal document summarization tool** that utilizes **Hugging Face’s FLAN-T5 model** to process and generate concise summaries of lengthy legal texts. This project is built using **Python**, **AWS SageMaker**, **Hugging Face Transformers**, and **PyTorch**.  


## **Table of Contents**  

- [1. Prerequisites](#1-prerequisites)  
- [2. Installation & Setup](#2-installation--setup)  
- [3. Running the Summarization Locally](#3-running-the-summarization-locally)  
- [4. Deploying the Model on AWS SageMaker](#4-deploying-the-model-on-aws-sagemaker)  
- [5. Using the Deployed Model for Inference](#5-using-the-deployed-model-for-inference)  
- [6. Cleaning Up Resources](#6-cleaning-up-resources)  
- [7. Security Best Practices](#7-security-best-practices)  


---

## **1. Prerequisites**  

Before setting up the project, ensure you have the following:  

### **System Requirements:**  
- **OS:** Windows / macOS / Linux  
- **Python:** 3.9+  
- **Git:** Installed and configured  
- **Jupyter Notebook:** Installed (`pip install jupyter`)  

### **AWS Requirements:**  
- **AWS Account:** Required for Amazon SageMaker  
- **S3 Bucket:** Used for storing datasets and models  
- **IAM Role:** Ensure you have the following AWS permissions:  
  - `AmazonSageMakerFullAccess`  
  - `AmazonS3FullAccess`  
  - `IAMFullAccess` (if modifying permissions)  
- **AWS CLI:** Installed (`aws configure` for setup)  

---

## **2. Installation & Setup**  

### **A. Clone the Repository**  
To get started, clone the repository from GitHub:  
```bash
git clone https://github.com/your-username/legalease.git
cd legalease
```

### **B. Create a Virtual Environment**  
It’s recommended to use a virtual environment to avoid dependency conflicts:  
```bash
python -m venv legalease
```

### **C. Activate the Virtual Environment**  

- **Windows:**  
  ```bash
  legalease\Scripts\activate
  ```

- **Mac/Linux:**  
  ```bash
  source legalease/bin/activate
  ```

### **D. Install Dependencies**  
Once the virtual environment is activated, install all required packages:  
```bash
pip install -r requirements.txt
```

---

## **3. Running the Summarization Locally**  

To test the summarization model locally before deploying it to AWS:  

1. **Start Jupyter Notebook:**  
   ```bash
   jupyter notebook
   ```
2. Open `Deploy.ipynb` or `pipeline_download.ipynb`.  
3. Run the cells step by step to preprocess text and generate summaries.  

---

## **4. Deploying the Model on AWS SageMaker**  

### **A. Upload the Model to S3**  
Before deployment, upload the fine-tuned model to an S3 bucket:  
```bash
aws s3 cp model.tar.gz s3://your-bucket-name/
```

### **B. Deploy the Model on SageMaker**  
1. Open `pipeline_download.ipynb`.  
2. Run the cells to initialize and deploy the model using SageMaker’s `HuggingFaceModel`.  
3. Ensure the **IAM role** has the correct permissions.  

---

## **5. Using the Deployed Model for Inference**  

Once deployed, the model can be used for **real-time inference** via the SageMaker endpoint.  

### **A. Making Predictions via API**  
You can send input text to the model for summarization using Python:  
```python
import boto3
import json

sagemaker_runtime = boto3.client('sagemaker-runtime')

response = sagemaker_runtime.invoke_endpoint(
    EndpointName="your-endpoint-name",
    ContentType="application/json",
    Body=json.dumps({"text": "Your legal document text here"})
)

result = json.loads(response['Body'].read())
print(result)
```

### **B. Testing the Model Locally**  
Alternatively, you can test the summarization locally before deploying it:  
```python
from transformers import pipeline 

model_id = "google/flan-t5-large"
summarizer = pipeline("summarization", model=model_id)

text = "The Eiffel Tower is 324 meters tall and the tallest structure in Paris..."
result = summarizer(text)

print(result)
```

---

## **6. Cleaning Up Resources**  

To avoid AWS charges, delete the SageMaker endpoint once you’re done:  
```bash
aws sagemaker delete-endpoint --endpoint-name your-endpoint-name
```

---

## **7. Security Best Practices**  

- **Do NOT commit AWS credentials to GitHub.** Always use a `.env` file for sensitive information.  
- **Use IAM roles instead of access keys** where possible.  
- **Monitor AWS usage** to avoid unnecessary charges.  

---

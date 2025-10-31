# CST8911: Lab 2

## Name: Kylath Mamman George

## Student Number: 041198835

## Policy Error

I get this policy error using East US regions, so I will be using Canada Central or other regions to do this lab if necessary.

![Policy error](image-1.png)

### Step 1

Storage Account Overview:

![Storage account settings](image-4.png)

### Step 2

Function app settings chosen:

![Function App settings](image-3.png)

### Step 3

Azure Blob Storage Trigger chosen:

![Azure blob storage chosen](image-5.png)

Storage account from Step 1 chosen(I had to use the create new option to choose my storage account):

![storage account chosen](image-6.png)

Container "cst8911lab2congtainer" created in the storage account

![container in Storage account](image-11.png)

BlobTrigger1 created:

![Trigger created](image-7.png)

### Step 4

**Note: I could not figure out how to use the "Test/Run" to manually test this specific trigger. I have used it in the past for HTTP triggers but nothing I tried seemed to work for this function. I am aware it is a useful tool to use to test, maybe I am missing something obvious here, however I can see that the trigger works with the following screenshots. In the code, I output some log statements, showing the file name and file size which match the output in the logstream provided. In function.json, the path of the container being monitored is also provided and matches my container name in my storage account.**

**I tried to do some research on this because the Azure Blob Storage trigger seems to be event driven and again maybe I am missing something simple but this article seems to point to non-HTTP triggers requiring a second trigger to simulate a blob being uploaded(?): <https://learn.microsoft.com/en-us/azure/azure-functions/functions-manually-run-non-http?tabs=azure-portal>.**

Screenshot of the code of the function:

![Function code](image-8.png)

Screenshot of function.json:

AzureWebJobsStorage is the connection to the container specified in function.json.

![function.json](image-9.png)

Screenshot of the uploaded file in the container:

![container upload](image-12.png)

Screenshot of the output from logstreams in Monitoring:

![Logstream of output](image-10.png)

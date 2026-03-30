#AI-Driven Serverless Image Processing Pipeline
**Developer:** Abdullah Ahmed  
**Region:** `eu-central-1` (Frankfurt)

---

## 1. Solution Architecture
The architecture follows a fully decoupled, serverless pattern to handle image uploads, automated resizing, and AI-driven metadata extraction.

![Architecture Diagram](./assets/architecture-diagram.png)

### Architectural Workflow:
1.  **Handshake:** The Client (Browser) requests a secure upload URL from **Amazon API Gateway**.
2.  **Authorization:** A **Lambda function** (`GetPresignedUrl`) generates an S3 Presigned URL, allowing the client to upload safely without AWS credentials.
3.  **Ingestion:** The Client uploads the raw image directly to the **Source S3 Bucket**.
4.  **Trigger:** S3 detects the new object and triggers the **ImageProcessor Lambda** via an Event Notification.
5.  **Processing:** The Lambda installs the **Pillow** library in the `/tmp` directory, resizes the image, and saves it to the **Destination S3 Bucket**.
6.  **Intelligence:** The Lambda calls **Amazon Rekognition** to identify objects and labels within the image.
7.  **Persistence:** All metadata (Labels, S3 paths, and Timestamps) is stored in **Amazon DynamoDB**.

---

## 2. Technical Stack
| Service | Role |
| :--- | :--- |
| **Frontend** | HTML5, CSS3, JavaScript (Vanilla) |
| **API Layer** | Amazon API Gateway (HTTP API) |
| **Compute** | AWS Lambda (Python 3.12) |
| **Storage** | Amazon S3 (Standard Storage Class) |
| **AI / ML** | Amazon Rekognition |
| **Database** | Amazon DynamoDB (NoSQL) |

---

## 3. Deployment & Configuration
### Prerequisites
* **IAM Role:** A single execution role (`ImageProcessorRole-Frankfurt`) with the following managed policies:
    * `AmazonS3FullAccess`
    * `AmazonRekognitionFullAccess`
    * `AmazonDynamoDBFullAccess`
    * `CloudWatchLogsFullAccess`

### Key Settings
* **Lambda Timeout:** The `ImageProcessor` function is configured with a **1-minute timeout** to account for the runtime installation of the `Pillow` library.
* **S3 CORS:** The source bucket is configured with a CORS policy to allow `PUT` and `POST` methods from the frontend origin.
* **Signature Version:** The Boto3 clients are forced to use `s3v4` to comply with the strict security requirements of the Frankfurt region.

---

## 4. Repository Structure
```text
.
├── assets/
│   └── architecture-diagram.png    # Lucidchart Export
├── frontend/
│   └── index.html                  # Client-side Application
├── lambda/
│   ├── get_presigned_url.py        # API Integration Logic
│   └── image_processor.py          # Processing & AI Logic
└── README.md                       # Project Documentation

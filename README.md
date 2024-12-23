# Secured File Sharing System

## Overview
This project implements a secure file sharing system using the open-source Gokapi API. It is designed to facilitate safe and efficient file sharing within organizations, ensuring that only authorized personnel can access and manage files.

## Features
- **Secure File Storage**: Utilizes Amazon S3 for reliable and secure file storage.
- **User Authentication**: Implements Google OpenID Connect (OIDC) for user authentication, allowing only verified users from the organization to access the system.
- **Role-Based Access Control**: Manages user permissions effectively to ensure appropriate access levels.
- **Temporary Public URLs**: Generates temporary public URLs for file downloads, enhancing security by limiting access duration.

## Technologies Used
- **Gokapi**: Open-source API for file sharing.
- **Docker Compose**: For container orchestration and service management.
- **Nginx**: As a reverse proxy to map the service with a custom domain.
- **Amazon EC2**: Hosting environment for the application.
- **Amazon S3**: Storage solution for files.
- **Google OIDC**: Authentication mechanism for user verification.

## Deployment Steps

### 1. Setup EC2 Instance
Launch an Amazon EC2 instance, attach an static ip to this instance and configure it for hosting the application.

### 2. Domain Mapping
Map a custom domain to the EC2 instance using Nginx as a reverse proxy.

### 3. Create an S3 Bucket
1. Sign in to the AWS Management Console.
2. Navigate to **S3** under **Storage & Content Delivery**.
3. Click on **Create Bucket**.
4. Enter a unique bucket name (e.g., `your-bucket-name`).
5. Choose an AWS region (e.g., Oregon).
6. Ensure that "Block all public access" is enabled.
7. Click on **Create Bucket**.

### 4. Create an IAM User
1. In the AWS Management Console, navigate to **IAM** (Identity and Access Management).
2. Click on **Users**, then select **Add user**.
3. Enter a username and grant the user only cli access.
4. Click on **Next: Permissions**.
5. Choose **Attach existing policies directly**, then select the policy that grants full access to only your newly created S3 bucket (or create a new inline policy if you haven't created a lready).
6. Click on **Next: Tags**, then click on **Next: Review**.
7. Click on **Create user**, and note down the Access Key ID and Secret Access Key.

### 5. Docker Setup
Create a `docker-compose.yml` file with the following content:

     ```
     version: '3'
     services:
       gokapi:
         image: f0rc3/gokapi:latest
         ports:
           - "53842:53842"
         volumes:
           - gokapi-data:/app/data
           - gokapi-config:/app/config
         environment:
           - TZ=UTC
           - GOKAPI_S3_BUCKET=your-bucket-name
           - GOKAPI_S3_ACCESS_KEY=your-access-key-id
           - GOKAPI_S3_SECRET_KEY=your-secret-access-key
     volumes:
       gokapi-data:
       gokapi-config:
       
     ```


     
  Replace `your-bucket-name`, `your-access-key-id`, and `your-secret-access-key` with your actual values.

  Run Docker Compose to start the service:
  docker-compose up -d


### 6. Configure nginx proxy for your domain name on your EC2 server and map your domain with the port 53842 or the port on which you have exposed the api in your docker compose file.


### 7. Configure Google OIDC
Set up Google OIDC for authentication, ensuring only organizational users can log in.

### 8. Connect to S3
Ensure that Gokapi is configured to use Amazon S3 as its storage backend using the IAM user credentials created earlier.

### 9. Start sharing your files securely! 
      - Now open the domain / URL we have mapped to see the GUI of the gokapi
      - Upload the file you want to share and at the time of upload set a password so that the user who wants to access this file will need to enter this password.
      - Now it will generate a temporary URL for this file
      - Share this URL with the user and boom! They will be able to access and download that file using the password we have set.


## For reference, find the attached screenshots in the same repo.     


## Acknowledgements
- [Gokapi](https://gokapi.readthedocs.io/en/latest/) - For providing an excellent open-source API for file sharing.
- [Amazon Web Services](https://aws.amazon.com/) - For reliable cloud infrastructure and services.
- [Google Developers](https://developers.google.com/identity/protocols/oauth2/openid-connect) - For documentation on OpenID Connect.

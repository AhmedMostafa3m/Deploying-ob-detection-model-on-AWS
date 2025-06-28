AWS EC2 Object Detection with YOLOv8
This repository demonstrates how to launch an AWS EC2 instance (t3.micro, Linux), set it up, and deploy a simple object detection model using YOLOv8 for performing detection tasks on images. The example uses a pre-trained YOLOv8 model from Ultralytics to detect objects in a sample image.
Prerequisites

AWS account with access to EC2
Basic familiarity with Linux terminal commands
Python 3.8 or higher (available on EC2 instance)
An SSH client (e.g., PuTTY, OpenSSH, or terminal)
A sample image for testing (included in this repository as sample.jpg)

Repository Contents

object_detection.py: Python script to run YOLOv8 object detection
sample.jpg: Sample image for testing the object detection model
requirements.txt: Python dependencies for the project
This README file

Step-by-Step Instructions
Step 1: Launch an EC2 Instance

Log in to AWS Management Console:

Navigate to the AWS EC2 Dashboard.


Launch a New Instance:

Click Launch Instance.
Name your instance (e.g., ObjectDetectionInstance).
Choose Amazon Linux 2 AMI (or Amazon Linux 2023) as the operating system.
Select t3.micro as the instance type (eligible for free tier if applicable).
Create or select an existing key pair for SSH access (e.g., my-key-pair.pem).
Save the .pem file securely; you'll need it to SSH into the instance.


Under Network settings, allow:
SSH (port 22) for terminal access.
Optionally, HTTP (port 80) if you plan to host results via a web server.


Configure storage (default 8 GiB gp2 volume is sufficient for this demo).
Review and click Launch Instance.


Retrieve Public IP:

Once the instance is running, note its Public IPv4 address from the EC2 dashboard.



Step 2: Connect to the EC2 Instance

SSH into the Instance:

Open a terminal (or PuTTY on Windows).
Use the command:ssh -i /path/to/my-key-pair.pem ec2-user@<public-ip>

Replace /path/to/my-key-pair.pem with the path to your key file and <public-ip> with the instance's public IP.
If prompted, accept the host key by typing yes.


Update the System:

Run the following commands to update the Amazon Linux system:sudo yum update -y





Step 3: Set Up the Environment

Install Python and Dependencies:

Amazon Linux 2 typically comes with Python 3. Verify with:python3 --version


Install pip if not already present:sudo yum install python3-pip -y


Install git to clone the repository:sudo yum install git -y




Clone the Repository:

Clone this repository to the EC2 instance:git clone https://github.com/<your-username>/<your-repo-name>.git
cd <your-repo-name>




Install Python Dependencies:

Install required Python packages:pip3 install -r requirements.txt

This installs ultralytics (for YOLOv8) and other dependencies.



Step 4: Run the Object Detection Model

Upload a Sample Image (Optional):

The repository includes sample.jpg. If you want to use your own image:
From your local machine, use SCP to upload an image:scp -i /path/to/my-key-pair.pem /local/path/to/image.jpg ec2-user@<public-ip>:/home/ec2-user/<your-repo-name>/


Replace paths and <public-ip> accordingly.




Run the Object Detection Script:

Execute the Python script to perform object detection:python3 object_detection.py


The script uses a pre-trained YOLOv8n model to detect objects in sample.jpg and saves the results as output.jpg in the same directory.


View Results:

The output image (output.jpg) contains bounding boxes and labels for detected objects.
Download the result to your local machine using SCP:scp -i /path/to/my-key-pair.pem ec2-user@<public-ip>:/home/ec2-user/<your-repo-name>/output.jpg /local/path/


Open output.jpg on your local machine to view the detection results.



Step 5: Clean Up

Stop or Terminate the Instance:

To avoid charges, stop or terminate the EC2 instance from the AWS Console:
Go to the EC2 Dashboard, select your instance, and choose Stop or Terminate.
Note: Terminating deletes the instance permanently; stopping allows you to restart it later.




Delete Key Pair and Other Resources (if no longer needed):

Remove the key pair from the EC2 Dashboard if you don’t plan to reuse it.
Check for unused EBS volumes or snapshots to avoid unexpected charges.



Python Script
Below is the Python script (object_detection.py) used for object detection with YOLOv8:

from ultralytics import YOLO
import cv2

Load the pre-trained YOLOv8n model
model = YOLO("yolov8n.pt")  # Nano model for lightweight deployment
Load the input image
image_path = "sample.jpg"image = cv2.imread(image_path)
Perform object detection
results = model(image)
Draw bounding boxes and labels on the image
annotated_image = results[0].plot()
Save the output image
output_path = "output.jpg"cv2.imwrite(output_path, annotated_image)print(f"Object detection complete. Output saved to {output_path}")

# AWS EC2 Object Detection with YOLOv8

This repository demonstrates how to launch an AWS EC2 instance (t3.micro, Linux), set it up, and deploy a simple object detection model using YOLOv8 for performing detection tasks on images. The example uses a pre-trained YOLOv8 model from Ultralytics to detect objects in a sample image.

## Prerequisites

- AWS account with access to EC2
- Basic familiarity with Linux terminal commands
- Python 3.8 or higher (available on EC2 instance)
- An SSH client (e.g., PuTTY, OpenSSH, or terminal)
- A sample image for testing (included in this repository as `sample.jpg`)

## Repository Contents

- `object_detection.py`: Python script to run YOLOv8 object detection
- `sample.jpg`: Sample image for testing the object detection model
- `requirements.txt`: Python dependencies for the project
- This README file

## Step-by-Step Instructions

### Step 1: Launch an EC2 Instance

1. **Log in to AWS Management Console**:
   - Navigate to the [AWS EC2 Dashboard](https://console.aws.amazon.com/ec2/).

2. **Launch a New Instance**:
   - Click **Launch Instance**.
   - Name your instance (e.g., `ObjectDetectionInstance`).
   - Choose **Amazon Linux 2 AMI** (or Amazon Linux 2023) as the operating system.
   - Select **t3.micro** as the instance type (eligible for free tier if applicable).
   - Create or select an existing **key pair** for SSH access (e.g., `my-key-pair.pem`).
     - Save the `.pem` file securely; you'll need it to SSH into the instance.
   - Under **Network settings**, allow:
     - SSH (port 22) for terminal access.
     - Optionally, HTTP (port 80) if you plan to host results via a web server.
   - Configure storage (default 8 GiB gp2 volume is sufficient for this demo).
   - Review and click **Launch Instance**.

3. **Retrieve Public IP**:
   - Once the instance is running, note its **Public IPv4 address** from the EC2 dashboard.

### Step 2: Connect to the EC2 Instance

1. **SSH into the Instance**:
   - Open a terminal (or PuTTY on Windows).
   - Use the command:
     ```bash
     ssh -i /path/to/my-key-pair.pem ec2-user@<public-ip>
     ```
     Replace `/path/to/my-key-pair.pem` with the path to your key file and `<public-ip>` with the instance's public IP.
   - If prompted, accept the host key by typing `yes`.

2. **Update the System**:
   - Run the following commands to update the Amazon Linux system:
     ```bash
     sudo yum update -y
     ```

### Step 3: Set Up the Environment

1. **Install Python and Dependencies**:
   - Amazon Linux 2 typically comes with Python 3. Verify with:
     ```bash
     python3 --version
     ```
   - Install `pip` if not already present:
     ```bash
     sudo yum install python3-pip -y
     ```
   - Install `git` to clone the repository:
     ```bash
     sudo yum install git -y
     ```

2. **Clone the Repository**:
   - Clone this repository to the EC2 instance:
     ```bash
     git clone https://github.com/<your-username>/<your-repo-name>.git
     cd <your-repo-name>
     ```

3. **Install Python Dependencies**:
   - Install required Python packages:
     ```bash
     pip3 install -r requirements.txt
     ```
     This installs `ultralytics` (for YOLOv8) and other dependencies.

### Step 4: Run the Object Detection Model

1. **Upload a Sample Image (Optional)**:
   - The repository includes `sample.jpg`. If you want to use your own image:
     - From your local machine, use SCP to upload an image:
       ```bash
       scp -i /path/to/my-key-pair.pem /local/path/to/image.jpg ec2-user@<public-ip>:/home/ec2-user/<your-repo-name>/
       ```
     - Replace paths and `<public-ip>` accordingly.

2. **Run the Object Detection Script**:
   - Execute the Python script to perform object detection:
     ```bash
     python3 object_detection.py
     ```
   - The script uses a pre-trained YOLOv8n model to detect objects in `sample.jpg` and saves the results as `output.jpg` in the same directory.

3. **View Results**:
   - The output image (`output.jpg`) contains bounding boxes and labels for detected objects.
   - Download the result to your local machine using SCP:
     ```bash
     scp -i /path/to/my-key-pair.pem ec2-user@<public-ip>:/home/ec2-user/<your-repo-name>/output.jpg /local/path/
     ```
   - Open `output.jpg` on your local machine to view the detection results.

### Step 5: Clean Up

1. **Stop or Terminate the Instance**:
   - To avoid charges, stop or terminate the EC2 instance from the AWS Console:
     - Go to the EC2 Dashboard, select your instance, and choose **Stop** or **Terminate**.
     - Note: Terminating deletes the instance permanently; stopping allows you to restart it later.

2. **Delete Key Pair and Other Resources** (if no longer needed):
   - Remove the key pair from the EC2 Dashboard if you don’t plan to reuse it.
   - Check for unused EBS volumes or snapshots to avoid unexpected charges.

## Python Script

Below is the Python script (`object_detection.py`) used for object detection with YOLOv8:


from ultralytics import YOLO
import cv2

# Load the pre-trained YOLOv8n model
model = YOLO("yolov8n.pt")  # Nano model for lightweight deployment

# Load the input image
image_path = "sample.jpg"
image = cv2.imread(image_path)

# Perform object detection
results = model(image)

# Draw bounding boxes and labels on the image
annotated_image = results[0].plot()

# Save the output image
output_path = "output.jpg"
cv2.imwrite(output_path, annotated_image)
print(f"Object detection complete. Output saved to {output_path}")


## Requirements File

The `requirements.txt` file lists the necessary Python packages:


ultralytics==8.0.196
opencv-python==4.8.1.78


## Notes

- **Model Choice**: This demo uses YOLOv8n (nano), a lightweight model suitable for t3.micro’s limited resources. For better accuracy, you can switch to larger models (e.g., `yolov8m.pt`) if using a more powerful instance.
- **Performance**: The t3.micro instance has limited CPU and memory (2 vCPUs, 1 GiB RAM), so processing large images or complex models may be slow.
- **Cost**: Ensure you’re within the AWS Free Tier limits or monitor costs in the AWS Billing Dashboard.
- **Security**: Keep your `.pem` key file secure and avoid exposing unnecessary ports in the EC2 security group.
- **Sample Image**: Replace `sample.jpg` with your own image for custom detection tasks. Ensure the image is in a compatible format (e.g., JPG, PNG).

## Troubleshooting

- **SSH Connection Issues**: Verify the security group allows SSH (port 22) and check the `.pem` file permissions (`chmod 400 my-key-pair.pem`).
- **Python Errors**: Ensure all dependencies are installed correctly (`pip3 install -r requirements.txt`).
- **Model Not Found**: The script downloads `yolov8n.pt` automatically on first run. Ensure the EC2 instance has internet access.
- **Out of Memory**: If the instance crashes, consider using a larger instance type (e.g., t3.small) or optimizing the image size.

## Contributing

Feel free to fork this repository, submit issues, or create pull requests to improve the demo!

## License

This project is licensed under the MIT License.

</xaiArtifact>

This README provides a clear, step-by-step guide to launching an EC2 instance, setting up the environment, and deploying a YOLOv8 object detection model. The Python script (`object_detection.py`) uses Ultralytics YOLOv8 for simplicity and efficiency, and the `requirements.txt` ensures all dependencies are specified. You’ll need to include a sample image (`sample.jpg`) in the repository for testing, which users can replace with their own images.

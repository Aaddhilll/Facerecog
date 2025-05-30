 import cv2
from libcamera import *
import os
import time

# Function to capture an image with optional filename
def capture_image(filename=None):
    """
    Captures a single image using the camera and saves it with the 
    provided filename (or "image.jpg" by default).
    """
    camera = Camera()
    # ... (Optional camera configuration)
    stream = camera.create_still_stream()

    if filename:
        # Save image with provided filename
        stream.capture(filename)
    else:
        # Save image as "image.jpg" (default)
        stream.capture("image.jpg")

    camera.close()

# Function to detect faces in an image and perform attendance marking
def detect_faces(image_path, student_data):
    """
    This function detects faces in an image and performs attendance marking.

    Args:
        image_path (str): Path to the captured image.
        student_data (dict): Dictionary containing student information (replace with your implementation)
    """
    img = cv2.imread(image_path)
    gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)  # Convert to grayscale for detection

    # Load the Haar cascade for face detection (replace 'haarcascade_frontalface_default.xml' with your model if different)
    face_cascade = cv2.CascadeClassifier('haarcascade_frontalface_default.xml')

    # Detect faces
    faces = face_cascade.detectMultiScale(gray, scaleFactor=1.1, minNeighbors=5)

    # Process detected faces
    for (x, y, w, h) in faces:
        cv2.rectangle(img, (x, y), (x+w, y+h), (255, 0, 0), 2)  # Draw rectangle around faces

        # Replace with your chosen attendance marking logic (example using student ID image comparison)
        identified = False
        for student_id, student_image_path in student_data.items():
            # Load student ID image for comparison (replace with your image loading logic)
            student_img = cv2.imread(student_image_path)
            # Implement image comparison (replace with your chosen method)
            # Here's a placeholder using template matching (adjust as needed)
            result = cv2.matchTemplate(student_img, gray[y:y+h, x:x+w], cv2.TM_CCOEFF_NORMED)
            min_val, max_val, min_loc, max_loc = cv2.minMaxLoc(result)
            if max_val > 0.8:  # Adjust threshold based on your image comparison method
                identified = True
                # Mark attendance for identified student (replace with your method)
                print(f"Student ID: {student_id} - Attendance Marked")
                # Update attendance data in your chosen method (e.g., text file, database)
                break  # Exit loop if student is identified

        if not identified:
            print("Unknown Face Detected")

    # Optional: Display the image with detected faces (for testing purposes)
    # cv2.imshow('Image with Faces', img)
    # cv2.waitKey(0)
    # cv2.destroyAllWindows()

# Replace with your chosen method for storing student data (example using a dictionary)
student_data = {
    "1234": "student_1.jpg",  # Student ID : Path to student ID image
    "5678": "student_2.jpg"
}

# Create a directory to store captured images (if it doesn't exist)
image_dir = "attendance_images"
os.makedirs(image_dir, exist_ok=True)

# Main program flow
while True:
    # Capture image with timestamped filename
    timestamp = time.strftime("%Y-%m-%d_%H-%M-%S")
    filename = f"{image_dir}/{timestamp}.jpg"
    capture_image(filename)

    # Process captured image for face detection and attendance marking
    detect_faces(filename, student_data)

    # Capture at set intervals (adjust as needed)
    time.sleep(5)
    import cv2
import os
from datetime import datetime

# Directory to save images
image_dir = "attendance_images"
os.makedirs(image_dir, exist_ok=True)

# Function to capture images using webcam
def capture_images(image_dir, num_images=10):
    cap = cv2.VideoCapture(0)
    if not cap.isOpened():
        print("Error: Could not open webcam.")
        return
    
    for i in range(num_images):
        ret, frame = cap.read()
        if not ret:
            print("Error: Could not read frame.")
            continue
        
        timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
        image_path = os.path.join(image_dir, f"image_{timestamp}_{i}.jpg")
        
        cv2.imwrite(image_path, frame)
        print(f"Captured {image_path}")
    
    cap.release()

# Function to detect faces in the captured images
def detect_faces(image_dir):
    face_cascade = cv2.CascadeClassifier(cv2.data.haarcascades + 'haarcascade_frontalface_default.xml')
    
    for filename in os.listdir(image_dir):
        if filename.endswith(".jpg"):
            img_path = os.path.join(image_dir, filename)
            img = cv2.imread(img_path)
            gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
            
            faces = face_cascade.detectMultiScale(gray, scaleFactor=1.1, minNeighbors=5, minSize=(30, 30))
            
            for (x, y, w, h) in faces:
                cv2.rectangle(img, (x, y), (x+w, y+h), (255, 0, 0), 2)
            
            # Save the image with detected faces
            detected_image_path = os.path.join(image_dir, f"detected_{filename}")
            cv2.imwrite(detected_image_path, img)
            print(f"Processed {detected_image_path}")

# Main function to register attendance
def register_attendance():
    print("Starting attendance registration...")
    capture_images(image_dir)
    detect_faces(image_dir)
    print("Attendance registration completed.")

# Uncomment the below line to run the script
# register_attendance()
import subprocess

# Replace with your image paths
input_image = "/home/cdac/Downloads/imjgrscl.jpg"
output_image = "/home/cdac/grayscale_image.jpg"  # Choose an output filename

command = ["convert", input_image, "-grayscale", output_image]  # List of command arguments
subprocess.run(command)  # Execute the command
print("Image converted to grayscale successfully!")
import cv2

# Replace with your image paths
input_image = "/home/cdac/Downloads/imjgrscl.jpg"
output_image = "/home/cdac/grayscale_image.jpg"  # Choose an output filename

# Read the image in color
img = cv2.imread(input_image)

# Convert to grayscale
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)

# Save the grayscale image
cv2.imwrite(output_image, gray)
print("Image converted to grayscale successfully!")
sudo apt-get install build-essential cmake git pkg-config libjpeg-dev libtiff-dev libavcodec-dev libavformat-dev libswscale-dev libv4l-dev libxvidcore-dev libx264-dev libgtk-3-dev libatlas-base-dev gfortran
sudo apt-get install python3-pip
pip3 install --upgrade pip setuptools wheel
import sqlite3

# Connect to the SQLite database (or create it if it doesn't exist)
conn = sqlite3.connect('attendance.db')
cursor = conn.cursor()

# Create the attendance table
create_table_query = """
CREATE TABLE IF NOT EXISTS attendance (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    timestamp DATETIME DEFAULT CURRENT_TIMESTAMP
);
"""

# Execute the query
cursor.execute(create_table_query)

# Commit the changes and close the connection
conn.commit()
conn.close()

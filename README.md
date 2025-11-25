import os
import requests
from urllib.parse import urlsplit
import shutil

# Define file type categories and their respective extensions
file_types = {
    "Images": [".jpg", ".jpeg", ".png", ".gif", ".bmp", ".tiff"],
    "Documents": [".pdf", ".docx", ".txt", ".xlsx", ".pptx"],
    "Videos": [".mp4", ".mkv", ".avi", ".mov"],
    "Music": [".mp3", ".wav", ".aac"],
    "Archives": [".zip", ".tar", ".gz", ".rar"],
    "Other": []  # For other file types
}

# Function to create folders for each category
def create_folders(base_path):
    for category in file_types:
        folder_path = os.path.join(base_path, category)
        if not os.path.exists(folder_path):
            os.makedirs(folder_path)

# Function to identify the file type based on the extension
def get_file_category(filename):
    for category, extensions in file_types.items():
        if any(filename.lower().endswith(ext) for ext in extensions):
            return category
    return "Other"  # If file type is unknown, categorize as "Other"

# Function to download a file
def download_file(url, base_path):
    try:
        # Get the file name from the URL
        file_name = os.path.basename(urlsplit(url).path)
        file_category = get_file_category(file_name)
        
        # Create appropriate folder if it doesn't exist
        folder_path = os.path.join(base_path, file_category)
        if not os.path.exists(folder_path):
            os.makedirs(folder_path)
        
        # Download the file
        print(f"Downloading {file_name}...")
        response = requests.get(url, stream=True)
        response.raise_for_status()  # Check if the request was successful
        
        # Save the file to the corresponding folder
        file_path = os.path.join(folder_path, file_name)
        with open(file_path, 'wb') as file:
            shutil.copyfileobj(response.raw, file)
        
        print(f"Downloaded {file_name} to {folder_path}")
    except requests.exceptions.RequestException as e:
        print(f"Error downloading {url}: {e}")

# Main function to process the URLs
def main():
    base_path = input("Enter the path where files should be saved: ").strip()
    
    # Check if base path exists
    if not os.path.exists(base_path):
        print(f"Error: The directory '{base_path}' does not exist.")
        return
    
    # Create folders for categories
    create_folders(base_path)
    
    # List of URLs to download files from
    urls = [
        "https://example.com/file1.jpg",
        "https://example.com/file2.mp4",
        "https://example.com/file3.pdf",
        "https://example.com/file4.zip"
    ]
    
    # Loop through the URLs and download the files
    for url in urls:
        download_file(url, base_path)
    
    print("All files downloaded and organized.")

# Run the script
if __name__ == "__main__":
    main()

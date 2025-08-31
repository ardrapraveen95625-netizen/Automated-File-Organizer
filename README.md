# Automated-File-Organizer
import os
import shutil
import logging

# Setup logging
logging.basicConfig(
    filename="file_organizer.log",
    level=logging.INFO,
    format="%(asctime)s - %(levelname)s - %(message)s",
)

# Map extensions to folders
EXTENSION_MAP = {
    "Documents": [".pdf", ".docx", ".txt", ".doc", ".xlsx", ".pptx"],
    "Images": [".jpg", ".jpeg", ".png", ".gif", ".bmp", ".svg"],
    "Code": [".py", ".js", ".java", ".cpp", ".c", ".html", ".css", ".php"],
}


def get_category(extension):
    for category, extensions in EXTENSION_MAP.items():
        if extension.lower() in extensions:
            return category
    return "Others"


def get_unique_destination(dest_folder, filename):
    base, ext = os.path.splitext(filename)
    counter = 1
    new_filename = filename

    while os.path.exists(os.path.join(dest_folder, new_filename)):
        new_filename = f"{base}({counter}){ext}"
        counter += 1

    return os.path.join(dest_folder, new_filename)


def organize_files(target_dir):
    if not os.path.isdir(target_dir):
        logging.error(f"Directory does not exist: {target_dir}")
        print(f"Directory does not exist: {target_dir}")
        return

    logging.info(f"Starting to organize: {target_dir}")
    print(f"Organizing files in: {target_dir}")

    for item in os.listdir(target_dir):
        item_path = os.path.join(target_dir, item)

        if os.path.isfile(item_path):
            ext = os.path.splitext(item)[1]
            category = get_category(ext)
            category_folder = os.path.join(target_dir, category)

            try:
                os.makedirs(category_folder, exist_ok=True)
                dest_path = get_unique_destination(category_folder, item)
                shutil.move(item_path, dest_path)
                logging.info(f"Moved: {item_path} -> {dest_path}")
            except PermissionError:
                logging.warning(f"Permission denied: {item_path}")
            except Exception as e:
                logging.error(f"Error moving {item_path}: {e}")

    print("File organization complete.")
    logging.info("Organization complete.\n")


if __name__ == "__main__":
    # You can replace this path with any directory you want to organize
    directory_to_scan = input("Enter the directory path to organize: ").strip()
    organize_files(directory_to_scan)



# Product_Organizer_Pandas
A comprehensive tool for categorizing, processing, and enriching product data in Excel with a modern graphical interface.

✨ Features
✅ Automatic classification with custom categories, 🔑 Manage categories and keywords, 📊 Detailed reports in Excel,🌐 HTML generation with product carousels, 🎬 Download YouTube videos (individual/playlist), 🎵 Convert video to audio, 🎶 Audio mixing with background music, 📁 Extract media URLs from various files,  Select Excel file, Configure categories and keywords, Classify products, Export reports or HTML, Download videos or add narration ⚠️ Requires FFmpeg installed

📸 Code Snippets - Product Category Organizer 

1. 🏗️ Imports and Initial Structure

<img width="333" height="224" alt="image" src="https://github.com/user-attachments/assets/93be04d5-7b87-413a-8108-6a2350dcf2e4" />

2. 📊 Product Classification

<img width="577" height="213" alt="image" src="https://github.com/user-attachments/assets/6257e6b3-1c91-48de-962e-528f5254239b" />

3. 🔄 Main Processing

<img width="655" height="325" alt="image" src="https://github.com/user-attachments/assets/d3e21d32-fae5-4641-876c-d6b4917c68cc" />

4. 🌐 YouTube Extraction

   <img width="529" height="257" alt="image" src="https://github.com/user-attachments/assets/91a87c1d-e2a2-4666-ace8-c3aed9ac47c6" />

5. 🖥️ Graphical Interface

<img width="714" height="365" alt="image" src="https://github.com/user-attachments/assets/63297b01-2f03-4bf3-9e5e-51060bbcde21" />

6. 📊 HTML Carousel Generation

<img width="473" height="531" alt="image" src="https://github.com/user-attachments/assets/91602ce9-7bcf-485f-b1a5-14f28634805c" />

7. 📁 URL Extraction
   
<img width="487" height="501" alt="image" src="https://github.com/user-attachments/assets/68c269ca-69c4-4e5f-9baf-dd800e0ef749" />




9. 🔄 Status Update


def update_task_status(task_key, status_text="pending", status_color="gray"):
    """Updates task status in the interface."""
    label = task_status_labels.get(task_key)
    if label:
        label.config(text=status_text, fg=status_color)
    window.update_idletasks()

# Available statuses
task_definitions = {
    "file_selection": "1. File Selection:",
    "pre_analysis": "2. Pre-analysis of Categories:",
    "main_processing": "3. Product Classification:",
    "video_download": "4. Video Download:",
    "audio_generation": "5. Audio Generation:",
}

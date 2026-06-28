# Product_Organizer_Pandas
A comprehensive tool for categorizing, processing, and enriching product data in Excel with a modern graphical interface.

✨ Features
✅ Automatic classification with custom categories, 🔑 Manage categories and keywords, 📊 Detailed reports in Excel,🌐 HTML generation with product carousels, 🎬 Download YouTube videos (individual/playlist), 🎵 Convert video to audio, 🎶 Audio mixing with background music, 📁 Extract media URLs from various files,  Select Excel file, Configure categories and keywords, Classify products, Export reports or HTML, Download videos or add narration ⚠️ Requires FFmpeg installed




📸 Code Snippets - Product Category Organizer 




1. 🏗️ Imports and Initial Structure


<img width="333" height="224" alt="image" src="https://github.com/user-attachments/assets/93be04d5-7b87-413a-8108-6a2350dcf2e4" />






<p></p>


2. 📊 Product Classification

<img width="577" height="213" alt="image" src="https://github.com/user-attachments/assets/6257e6b3-1c91-48de-962e-528f5254239b" />

<p></p>

3. 🔄 Main Processing

<img width="655" height="325" alt="image" src="https://github.com/user-attachments/assets/d3e21d32-fae5-4641-876c-d6b4917c68cc" />


4. 🌐 YouTube Extraction

   <img width="529" height="257" alt="image" src="https://github.com/user-attachments/assets/91a87c1d-e2a2-4666-ace8-c3aed9ac47c6" />

5. 🖥️ Graphical Interface

# Interface with ttkbootstrap
style = Style(theme="flatly")
window = style.master
window.title("Product Category Organizer")
window.geometry("1200x750")

# Header buttons
create_header_button(button_frame, "Select File", select_file, "#3F51B5", 0, 0)
create_header_button(button_frame, "Add Category", add_category, "#FFC107", 0, 2)
create_header_button(button_frame, "Process", process_file, "#4CAF50", 2, 3)
create_header_button(button_frame, "HTML (Videos)", generate_html_only_videos, "#FF9800", 1, 1)

# Progress bar
progress_bar = Progressbar(main_frame, length=400, 
                          mode='determinate', bootstyle="success-striped")
progress_bar.pack(pady=20)




8. 📊 HTML Carousel Generation


def execute_html_generation_in_thread(media_type, autoplay_delay_ms, save_path, status_key):
    """Generates HTML file with product carousel."""
    df = pd.read_excel(file_path)
    html_items = []
    
    for index, row in df.iterrows():
        name = str(row[product_name_column])
        description = str(row[description_column])
        access_link = str(row[access_link_column])
        
        # Check if it's YouTube
        youtube_embed_url, youtube_video_id = get_youtube_embed_url(current_video_link)
        
        if youtube_embed_url:
            media_content = f"""
                <iframe src="{youtube_embed_url}" frameborder="0" 
                        allow="accelerometer; autoplay; encrypted-media" 
                        loading="lazy"></iframe>
            """
        
        item_html = f"""
            <div class="video-container{' active' if index == 0 else ''}">
                <div class="media-area">{media_content}</div>
                <div class="video-description">
                    <h2>{name}</h2>
                    <p>{description}</p>
                    <a href="{access_link}" class="buy-button">Buy</a>
                </div>
            </div>
        """
        html_items.append(item_html)
    
    # Save HTML
    with open(save_path, "w", encoding="utf-8") as f:
        f.write(full_html)





9. 📁 URL Extraction



def execute_url_extraction_in_thread(excel_path, text_path, output_excel_path):
    """Extracts media URLs from Excel and text/Word files."""
    # Regex for image URLs
    image_url_pattern = re.compile(
        r'https?://[^\s/$.?#].[^\s]*?\.(?:jpg|jpeg|png|gif|bmp|webp|tiff|svg|ico)',
        re.IGNORECASE
    )
    
    # Regex for video URLs
    video_url_pattern = re.compile(
        r'https?://(?:www\.)?(?:youtube\.com/watch\?v=|youtu\.be/|vimeo\.com/)[^\s]+',
        re.IGNORECASE
    )
    
    # Read Excel and extract URLs from all columns
    df_excel_base = pd.read_excel(excel_path)
    all_image_urls = set()
    all_video_urls = set()
    
    for column in df_excel_base.columns:
        for item in df_excel_base[column].dropna().astype(str):
            found_images = image_url_pattern.findall(item)
            found_videos = video_url_pattern.findall(item)
            all_image_urls.update(found_images)
            all_video_urls.update(found_videos)
    
    # Save result
    df_output = pd.concat([df_excel_base, df_extracted_urls], axis=1)
    df_output.to_excel(output_excel_path, index=False)




10. 🔄 Status Update


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

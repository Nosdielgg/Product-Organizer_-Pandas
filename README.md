# Product_Organizer_Pandas
A comprehensive tool for categorizing, processing, and enriching product data in Excel with a modern graphical interface.

✨ Features
✅ Automatic classification with custom categories, 🔑 Manage categories and keywords, 📊 Detailed reports in Excel,🌐 HTML generation with product carousels, 🎬 Download YouTube videos (individual/playlist), 🎵 Convert video to audio, 🎶 Audio mixing with background music, 📁 Extract media URLs from various files,  Select Excel file, Configure categories and keywords, Classify products, Export reports or HTML, Download videos or add narration ⚠️ Requires FFmpeg installed




📸 Code Snippets - Product Category Organizer 




1. 🏗️ Imports and Initial Structure


import pandas as pd
import tkinter as tk
from tkinter import filedialog, messagebox
from ttkbootstrap import Style
from ttkbootstrap import Progressbar
import yt_dlp
from gtts import gTTS
import subprocess
from pydub import AudioSegment






2. 📊 Product Classification

def classify_products(description):
    """
    Classifies the product description based on registered keywords.
    """
    desc = str(description).lower()
    for category, keywords in keywords_dict.items():
        if any(keyword in desc for keyword in keywords):
            return category
    return 'Others'






   
4. 🔄 Main Processing

def process_in_thread():
    """Reads the file, classifies products, and saves to separate sheets."""
    df = pd.read_excel(file_path)
    description_column = select_column(df)
    
    for i, index in enumerate(df.index):
        df.at[index, 'Category'] = classify_products(df.at[index, description_column])
        progress_bar['value'] = i + 1
        progress_bar.update()
    
    with pd.ExcelWriter(save_path) as writer:
        for category in all_categories:
            df_category = df[df['Category'] == category]
            df_category.to_excel(writer, sheet_name=category[:31], index=False)





   
5. 🌐 YouTube Extraction

def get_youtube_embed_url(url):
    """Extracts YouTube video ID and returns embed URL."""
    youtube_regex = (
        r'(https?://)?(www\.)?'
        r'(youtube|youtu|youtube-nocookie)\.(com|be)/'
        r'(watch\?v=|embed/|v/|.+\?v=)?([^&=%\?]{11})')
    match = re.match(youtube_regex, url)
    if match:
        video_id = match.group(6)
        return f"https://www.youtube.com/embed/{video_id}", video_id
    return None, None




   
6. 🎬 Video Download

def execute_download_in_thread(df, link_column, destination_folder, playlist_url=None):
    """Downloads videos using yt-dlp."""
    ydl_opts = {
        'outtmpl': os.path.join(destination_folder, '%(title)s.%(ext)s'),
        'ignoreerrors': True,
        'format': 'bestvideo[ext=mp4]+bestaudio[ext=m4a]/best[ext=mp4]/best',
    }
    
    with yt_dlp.YoutubeDL(ydl_opts) as ydl:
        for video_url in df[link_column]:
            ydl.download([video_url])



   
7. 🎵 Audio Generation (Narration)

def execute_audio_generation_in_thread(text_content, srt_filepath, lang_code, 
                                      video_filepath, background_music_filepath):
    """Generates audio narration and combines with video using FFmpeg."""
    tts = gTTS(text=text_content, lang=lang_code, slow=False)
    tts.save(temp_speech_audio_file)
    
    speech_audio = AudioSegment.from_file(temp_speech_audio_file)
    
    if background_music_filepath:
        background_music = AudioSegment.from_file(background_music_filepath)
        background_music = background_music[:len(speech_audio)]
        mixed_audio = background_music.overlay(speech_audio)
        mixed_audio.export(temp_final_audio_file, format="mp3")
    
    # Combine with video using FFmpeg
    ffmpeg_command = [
        'ffmpeg', '-i', video_filepath,
        '-i', audio_input_for_ffmpeg,
        '-c:v', 'copy', '-map', '0:v:0', '-map', '1:a:0',
        '-y', output_video_filepath
    ]
    subprocess.run(ffmpeg_command, check=True)


   
8. 🖥️ Graphical Interface

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

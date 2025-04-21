import tkinter as tk
from tkinter import filedialog
from mutagen.mp3 import MP3
import pygame
import os

root = tk.Tk()
root.geometry("512x700")
root.title("Levytskyi's Music Player")
pygame.mixer.init()

is_paused = True
song_length = 0
filename = ""
seek_offset = 0

song_title = tk.Label(root, text ="Now Playing: None")
song_title.grid()

def format_time(seconds):
    minutes = seconds // 60
    secs = seconds % 60
    return f"{minutes}:{secs:02}"

def choose_folder():
    global foldername, song_length, is_paused
    foldername = filedialog.askdirectory()
    if foldername:  
        display_list(foldername)    

def display_list(foldername):
    contents = os.listdir(foldername)
    music_list.delete(0, tk.END)
    for item in contents:
        music_list.insert(tk.END, item)

def choose_file():
    global filename, song_length, is_paused, seek_offset
    filename = filedialog.askopenfilename(initialdir='/media')
    if filename:
        pygame.mixer.music.load(filename)
        pygame.mixer.music.play()
        song_title.config(text="Now Playing: " + os.path.basename(filename))
        audio = MP3(filename)
        song_length = int(audio.info.length)
        time_slider.config(to=song_length)
        is_paused = False
        seek_offset = 0
        update_slider()

def pause_file():
    global is_paused
    pygame.mixer.music.pause()
    is_paused = True

def unpause_file():
    global is_paused
    pygame.mixer.music.unpause()
    is_paused = False

button_frame = tk.Frame(root)
button_frame.grid(sticky = 'w', pady=10)

folder_button = tk.Button(button_frame, text="open folder", command=choose_folder)
folder_button.grid(row=1, column=1, padx=5)

choose_button = tk.Button(button_frame,text="choose file", command=choose_file)
choose_button.grid(row=1, column=2, padx=5)

pause_button = tk.Button(button_frame, text="pause the music", command=pause_file)
pause_button.grid(row=1, column=3, padx=5)

unpause_button = tk.Button(button_frame, text="unpause", command=unpause_file)
unpause_button.grid(row=1, column=4, padx=5)

def preview_slider(event):
    global is_paused
    pygame.mixer.music.pause()
    value = time_slider.get()
    time_label.config(text=f"{format_time(value)} / {format_time(song_length)}")


def seek_music(event):
    global seek_offset, is_paused
    pygame.mixer.music.unpause()
    seek_offset = time_slider.get()
    pygame.mixer.music.play(start=seek_offset)
    is_paused = False

def update_slider():
    global current_pos
    if pygame.mixer.music.get_busy() and not is_paused:
        pos_ms = pygame.mixer.music.get_pos()
        if pos_ms != -1:
            current_pos = seek_offset + (pos_ms // 1000)
            time_slider.set(current_pos)
            time_label.config(text=f"{format_time(current_pos)} / {format_time(song_length)}")
    root.after(1000, update_slider)

time_slider = tk.Scale(root, from_=0, to=100, orient="horizontal", length=400, showvalue=0)
time_slider.grid()

time_slider.bind("<B1-Motion>", preview_slider)
time_slider.bind("<ButtonRelease-1>", seek_music)

time_label=tk.Label(root, text="00:00 / 00:00")
time_label.grid()

music_list = tk.Listbox(root, selectmode="single", height=20, width=30)
music_list.grid()

exit_button = tk.Button(root, text="exit", command=root.quit)
exit_button.grid()

def execute_command(event):
    global is_paused, song_length, filename, foldername, song_path, seek_offset
    selected_index = music_list.curselection()
    if selected_index:
        selected_song = music_list.get(selected_index)
        song_path = os.path.join(foldername, selected_song)
        pygame.mixer.music.load(song_path)
        pygame.mixer.music.play()
        song_title.config(text="Now Playing: " + os.path.basename(song_path))
        audio = MP3(song_path)
        song_length = int(audio.info.length)
        time_slider.config(to=song_length)
        is_paused = False
        seek_offset = 0
        update_slider()

music_list.bind("<ButtonRelease-1>", execute_command)

root.mainloop()

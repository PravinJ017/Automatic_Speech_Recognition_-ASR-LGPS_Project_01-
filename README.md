# Automatic Speech Recognition (ASR) for Transcription

This project provides a web-based system to transcribe speech from audio or video recordings into text using various Python libraries for audio processing, speech recognition, and web interfacing.

## Features

- **Upload Support**: Users can upload audio and video files in multiple formats.
- **Accurate Transcription**: Audio files are segmented for more accurate transcription.
- **Database Storage**: Transcripts are stored in a SQLite database.
- **Export to CSV**: Transcripts can be exported to a CSV file.
- **Database Management**: Option to clear the transcripts table in the database.

## Technologies Used

- **Backend**: Python, Flask, SQLite
- **Speech Recognition**: SpeechRecognition, pydub, moviepy
- **Frontend**: HTML, CSS
- **Deployment**: ngrok

## Setup Instructions

### Prerequisites

- Python 3.x
- pip (Python package installer)

### Install Dependencies

```bash
pip install Flask ngrok pyngrok SpeechRecognition pydub
```

Additionally, install `ffmpeg`:

```bash
sudo apt-get install ffmpeg
```

### Ngrok Setup

1. Sign up at [ngrok](https://ngrok.com/) and get your authentication token.
2. Authenticate ngrok with your token:

```bash
ngrok authtoken YOUR_NGROK_AUTH_TOKEN
```

### Initialize the Database

Before running the application, initialize the database:

```python
def init_db():
    conn = sqlite3.connect('transcripts.db')
    cursor = conn.cursor()
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS transcripts (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            video_name TEXT,
            transcript TEXT
        )
    ''')
    conn.commit()
    conn.close()

init_db()
```

### Running the Application

1. **Start the Flask App**:

```python
from flask import Flask, request, render_template_string, redirect, g
import speech_recognition as sr
from pydub import AudioSegment
import moviepy.editor as mp
import os
from pyngrok import ngrok
import sqlite3

app = Flask(__name__)
app.config['UPLOAD_FOLDER'] = 'uploads'
if not os.path.exists(app.config['UPLOAD_FOLDER']):
    os.makedirs(app.config['UPLOAD_FOLDER'])

# Define routes and handlers here

init_db()

public_url = ngrok.connect(5000).public_url
print(f' * ngrok tunnel "http://127.0.0.1:5000" -> "{public_url}"')
app.run()
```

2. **Access the Application**:

   After starting the Flask app, access it using the ngrok public URL provided in the terminal.

### Exporting Transcripts to CSV

To export the transcripts to a CSV file, use the following script:

```python
import sqlite3
import csv

def export_transcripts_to_csv():
    conn = sqlite3.connect('transcripts.db')
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM transcripts")
    rows = cursor.fetchall()
    column_names = [description[0] for description in cursor.description]

    with open('transcripts.csv', 'w', newline='', encoding='utf-8') as csv_file:
        csv_writer = csv.writer(csv_file)
        csv_writer.writerow(column_names)
        csv_writer.writerows(rows)

    conn.close()
    print("Data has been successfully exported to transcripts.csv")

if __name__ == "__main__":
    export_transcripts_to_csv()
```

### Clearing the Transcripts Table

To empty the transcripts table, use the following script:

```python
import sqlite3

def empty_transcripts_table():
    conn = sqlite3.connect('transcripts.db')
    cursor = conn.cursor()
    cursor.execute("DELETE FROM transcripts")
    conn.commit()
    conn.close()
    print("All records have been deleted from the transcripts table.")

if __name__ == "__main__":
    empty_transcripts_table()
```

## Contribution

Feel free to fork this project, submit issues, and pull requests. Contributions are welcome! For any questions or suggestions, you can reach out to me at pravinjaiswal1710@gmail.com.

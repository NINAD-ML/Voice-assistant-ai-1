import openai
import gradio as gr
import gspread
from oauth2client.service_account import ServiceAccountCredentials
from datetime import datetime
import pyttsx3
import tempfile
import os
import speech_recognition as sr

# === CONFIG ===
openai.api_key = 'your_openai_api_key'  # <-- Put your OpenAI key here

# Google Sheets API setup
scope = ["https://spreadsheets.google.com/feeds", "https://www.googleapis.com/auth/drive"]
creds = ServiceAccountCredentials.from_json_keyfile_name('credentials.json', scope)
client = gspread.authorize(creds)
spreadsheet = client.open("Voice Assistant Logs")

# Setup or create sheets
def get_or_create_sheet(sheet_name, headers):
    try:
        sheet = spreadsheet.worksheet(sheet_name)
    except gspread.exceptions.WorksheetNotFound:
        sheet = spreadsheet.add_worksheet(title=sheet_name, rows="1000", cols="20")
        sheet.append_row(headers)
    return sheet

transcript_sheet = get_or_create_sheet("Transcripts", ["Timestamp", "User ID", "Transcript"])
response_sheet = get_or_create_sheet("Responses", ["Timestamp", "User ID", "Response"])

# Initialize TTS engine
tts_engine = pyttsx3.init()

def transcribe_audio(audio_file_path):
    recognizer = sr.Recognizer()
    with sr.AudioFile(audio_file_path) as source:
        audio = recognizer.record(source)
    try:
        text = recognizer.recognize_google(audio)
        return text
    except Exception as e:
        return f"[Transcription error: {e}]"

def generate_gpt_response(prompt):
    try:
        completion = openai.ChatCompletion.create(
            model="gpt-3.5-turbo",
            messages=[{"role": "user", "content": prompt}]
        )
        return completion.choices[0].message.content.strip()
    except Exception as e:
        return f"Error generating response: {e}"

def log_to_sheets(user_id, transcript, response):
    timestamp = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    try:
        transcript_sheet.append_row([timestamp, user_id, transcript])
        response_sheet.append_row([timestamp, user_id, response])
    except Exception as e:
        print(f"Error logging to sheets: {e}")

def text_to_speech(text):
    with tempfile.NamedTemporaryFile(delete=False, suffix=".mp3") as tmpfile:
        tts_engine.save_to_file(text, tmpfile.name)
        tts_engine.runAndWait()
        return tmpfile.name

def voice_assistant(user_id, transcript):
    if not transcript or transcript.startswith("[Transcription error"):
        return transcript, None
    response = generate_gpt_response(transcript)
    log_to_sheets(user_id, transcript, response)
    audio_path = text_to_speech(response)
    return response, audio_path

def process(user_id, audio_path):
    if not user_id:
        return "Please enter user ID.", "", None
    if not audio_path:
        return "", "", None

    transcript = transcribe_audio(audio_path)
    if transcript.startswith("[Transcription error"):
        return transcript, "", None

    response, audio_file = voice_assistant(user_id, transcript)
    return transcript, response, audio_file

with gr.Blocks() as demo:
    gr.Markdown("# Voice Assistant with Transcript & Response Logging")
    user_id = gr.Textbox(label="User ID (e.g. sales rep name)", placeholder="Enter your user ID", lines=1)
    voice_input = gr.Audio(source="microphone", type="filepath", label="Speak now")
    transcript_output = gr.Textbox(label="Transcript", interactive=False)
    response_output = gr.Textbox(label="GPT Response", interactive=False)
    audio_output = gr.Audio(label="Response Audio", interactive=False)

    voice_input.change(process, inputs=[user_id, voice_input], outputs=[transcript_output, response_output, audio_output])

demo.launch()


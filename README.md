# Voice-assistant-ai-1

# Voice Assistant AI with Transcript and Response Logging

## Overview

This project implements an AI-powered voice assistant designed to handle voice input, generate intelligent responses using OpenAI's GPT-3.5-turbo model, and reply back via synthesized speech. It also logs both the user’s spoken transcripts and the AI-generated responses into separate sheets in a Google Sheets document for organized tracking.

The solution is intended to support multiple users distinguished by unique user IDs, enabling clean separation of logged data per user. The system features a simple web-based user interface built with Gradio, allowing voice recording directly from the browser and playback of AI responses.

---

## Features

- Voice Input: Captures user speech from the browser microphone and converts it to text using Google’s Speech Recognition service.
- AI Response Generation: Uses OpenAI’s GPT-3.5-turbo to generate conversational responses based on transcribed input.
- Text-to-Speech Output: Converts AI responses into audio for playback using a local TTS engine (pyttsx3).
- Logging: Separately logs user transcripts and AI responses with timestamps and user IDs into different worksheets in a Google Sheets spreadsheet.
- User Interface: Provides an intuitive web UI via Gradio, facilitating easy interaction without complex setup.
  
---

## Alignment with Company Role and Requirements

This project supports the following company goals and responsibilities:

- AI Tool Testing and Integration: Demonstrates effective use of generative AI models (OpenAI GPT) alongside open-source tools (SpeechRecognition, pyttsx3, Gradio).
- Workflow Automation: Implements a straightforward AI-driven voice workflow, combining speech-to-text, LLM processing, text-to-speech, and cloud-based logging.
- System Connectivity: Integrates multiple APIs and services, including Google Sheets for centralized data management.
- Prompt Engineering and Output Testing: Facilitates iterative testing of AI prompts and responses to improve quality.
- Documentation and Collaboration: Provides clear, maintainable code with accompanying documentation for internal team use and future enhancement.
- Scalability and Multi-user Support: Designed to scale with user identification and segregated data logging, supporting real-world multi-user deployment scenarios.

---

## Prerequisites

- Python 3.8 or higher
- OpenAI API key
- Google Cloud Service Account credentials JSON file with access to Google Sheets API
- Python packages: openai, gradio, gspread, oauth2client, pyttsx3, SpeechRecognition

---

## Installation and Setup

1. Clone or download this repository.
2. Place your Google service account credentials.json in the project directory.
3. Set your OpenAI API key in the code or as an environment variable.
4. Install required packages using:


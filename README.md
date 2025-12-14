# Doctor Feedback Voice Agent

A real-time, low-latency voice AI agent designed to automate post-consultation patient feedback collection. This project leverages **LiveKit** for WebRTC transport, **Sarvam AI** for region-specific speech processing (Indian English), and **Groq** for high-speed inference, creating a seamless conversational experience that integrates directly with data dashboards.

## Project Overview

This agent automates the critical loop of patient feedback retention. Instead of static forms, it engages patients in natural, voice-based conversations immediately after appointments. The system handles full-duplex audio streaming, voice activity detection, and state management to guide the conversation through a structured questionnaire before autonomously triggering a function call to persist structured data.

### Key Features

- **Real-time Full-Duplex Voice:** Built on WebRTC via LiveKit for sub-second latency.
- **Region-Specific Speech AI:** Utilizes Sarvam AI's `saaras` (STT) and `bulbul` (TTS) models optimized for Indian English accents and speech patterns.
- **High-Performance Inference:** Powered by Groq (Llama 3.3-70b) to ensure near-instantaneous conversational logic processing.
- **Structured Data Extraction:** Automatically converts unstructured voice conversations into structured JSON data using function calling.
- **Resilient Architecture:** Handles interruptions, turn-taking (VAD), and participant disconnections gracefully.

## Technical Architecture

The system follows a modular Worker/Agent pattern rather than a simple chained pipeline. This allows for granular control over the conversation loop and state management.

1. **Transport Layer:** LiveKit Server handles the SIP/WebRTC connection.
2. **Agent Worker:** A persistent Python process listens for job assignments via WebSocket.
3. **Conversation Loop:**
   - **VAD (Voice Activity Detection):** Silero VAD detects user speech vs. silence.
   - **STT (Speech-to-Text):** Sarvam AI transcodes audio streams to text.
   - **LLM (Reasoning):** Groq processes text and maintains context/state.
   - **TTS (Text-to-Speech):** Sarvam AI generates audio response.
4. **Tool Execution:** The LLM decides when to execute the `save_feedback` Python function based on conversation progress.

## Tech Stack

- **Core Framework:** Python 3.10+, LiveKit Agents SDK  
- **LLM Engine:** Groq (Llama 3.3-70b-versatile)  
- **Speech Services (STT/TTS):** Sarvam AI (Models: `saaras`, `bulbul:v2`)  
- **Voice Activity Detection:** Silero VAD  
- **Protocol:** WebRTC (High reliability, low latency)

## Installation & Setup

### Prerequisites

- Python 3.9 or higher  
- LiveKit Cloud Project (or self-hosted server)  
- API Keys for Groq and Sarvam AI  

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/doctor-feedback-agent.git
cd doctor-feedback-agent

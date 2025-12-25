# Doctor Feedback Voice Agent

A real-time, low-latency voice AI agent designed to automate post-consultation patient feedback collection. This project leverages **LiveKit** for WebRTC transport, **Sarvam AI** for region-specific speech processing (Indian English), and **Groq** for high-speed inference, creating a seamless conversational experience that integrates directly with data dashboards.

## Project Overview

This agent automates the critical loop of patient feedback retention. Instead of static forms, it engages patients in natural, voice-based conversations immediately after appointments. The system handles full-duplex audio streaming, voice activity detection, and state management to guide the conversation through a structured questionnaire before autonomously triggering a function call to persist structured data.
<img width="700" height="400" alt="Screenshot 2025-12-26 014221" src="https://github.com/user-attachments/assets/3afe1ea5-dcad-430f-939f-9357f9634670" />

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
```

### Environment File Structure

```env
# LiveKit Configuration
LIVEKIT_URL=wss://your-project.livekit.cloud
LIVEKIT_API_KEY=API_xxxxxxxxxxxx
LIVEKIT_API_SECRET=xxxxxxxxxxxxxxxx

# Inference & Speech Providers
GROQ_API_KEY=gsk_xxxxxxxxxxxx
SARVAM_API_KEY=sk_xxxxxxxxxxxx
```

### Dependancies Installation

```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
pip install -r requirements.txt
```

```
python agent.py dev
```


### Dispatch a Call

- **Option A (Playground):**  
  Go to the LiveKit Console Playground and create a room.  
  The agent will automatically join the room.

- **Option B (SIP/Telephony):**  
  If a SIP trunk is configured, dispatch a SIP call to a phone number using the LiveKit CLI.

---

## Data Persistence

The agent collects feedback on three parameters:

- **Communication Rating** (1–5)
- **Wait Time Perception**
- **Additional Comments**

Upon successful completion of the dialogue, the data is:

1. Logged to the console in structured JSON format  
2. Appended to `feedback_dashboard.json` for persistent storage

---

## Implementation Details

The core logic resides in the `DoctorFeedbackAgent` class.  
It uses the `AgentSession` context to manage plugins, conversation state, and tool execution.

### Function Calling Definition

```python
@function_tool
async def save_feedback(
    self,
    rating: Annotated[int, "Rating for communication (1-5)"],
    wait_time: Annotated[str, "The patient's perception of wait time"],
    comments: Annotated[str, "Final comments from the patient"]
) -> str:
    """Saves the collected patient feedback to the dashboard."""
    # Logic to persist data...
```

## Why Sarvam AI?

Standard TTS/STT models often struggle with non-Western accents.
Sarvam AI was chosen specifically for its high accuracy with Indian English, ensuring the medical agent remains accessible and effective for the target demographic.

## Why Groq?

Voice agents require extremely low Time-To-First-Byte (TTFB).
Groq’s LPU architecture provides the inference speed necessary to prevent “dead air” silence during conversations, maintaining a natural and fluid interaction.

## Future Roadmap

Database Integration:
Replace local JSON storage with PostgreSQL or Supabase

SIP Outbound Dialing:
Automate call triggering based on appointment scheduling software (e.g., cron jobs checking a calendar API)

Sentiment Analysis:
Add post-processing to tag feedback as Positive / Negative / Urgent

Fallback Handling:
Implement fallback logic for generic English models if Sarvam API latency spikes

---

<p align="center"><em>Crafted with an interest in real-time AI systems ~Jayanth</em></p>


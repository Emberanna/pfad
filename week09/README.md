# Week 09 — Networks and APIs

Five runnable examples covering the two protocols this week is about: **HTTP/REST**, where the
client asks and the server answers, and **WebSockets**, where either side can speak at any time.

> **Note (2026):** an earlier version of this README documented `speech_to_text_webrtc.py`,
> `simple_transcription_test.py`, `pygame_websocket.py` and a `week09/compose.yml`. Those files
> were never committed. The list below is what is actually in this folder.

## Setup

```bash
pip install -r requirements.txt
```

`pyaudio` needs PortAudio on the system first — `brew install portaudio` on macOS. It is only
required by `simple_audio_transcription.py`; the other four examples run without it.

## The files

| File | What it does |
|---|---|
| `fastapi_example.py` | A REST API. Defines a `MusicRequest` model with pydantic and exposes it over HTTP. Run with `uvicorn fastapi_example:app --reload`, then open <http://localhost:8000/docs> — FastAPI generates the interactive docs from your type hints, which is the point of the example. |
| `websocket_server_echo.py` | The smallest possible WebSocket server: accepts a connection and echoes back whatever it receives. Listens on `ws://localhost:8765`. |
| `websocket_server_echo_ping.py` | The same, plus a ping/pong keepalive — what you need when a connection has to survive being idle. |
| `websocket_client_example.py` | Connects to the echo server, sends what you type, prints what comes back. Run a server first, then this. |
| `simple_audio_transcription.py` | The one that combines everything: captures microphone audio with `pyaudio`, base64-encodes it, streams it over a WebSocket to a transcription service, and prints results as they arrive. Needs a working mic. |

## Try it

Two terminals. In the first:

```bash
python websocket_server_echo.py
```

In the second:

```bash
python websocket_client_example.py
```

Type something. Then the exercise from the lecture: **change the client to connect to a
classmate's machine instead of `localhost`.** You will need their IP address, you will need them
to bind the server to `0.0.0.0` rather than `localhost`, and you will probably meet a firewall.
Each of those is one layer of the stack from the slides, in the order the slides introduce them.

## Why WebSockets and not REST

REST is stateless: every request stands alone, the server forgets you between calls, and only the
client can start a conversation. That is exactly right for "give me the tide data" and exactly
wrong for "tell me the moment anyone else moves". A WebSocket keeps the connection open so either
side can send at any time — which is what you want for chat, multiplayer, live transcription, or
anything with a cursor in it.

The cost is that somebody now has to hold that connection open, and remember what it belongs to.
That is the whole reason stateful infrastructure is harder than stateless.

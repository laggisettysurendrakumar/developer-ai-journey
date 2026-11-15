# 08 — Multimodal Models: Images, Audio and Video

## What does multimodal mean?

A text-only model works with text input and output. A multimodal model can process or generate more than one kind of content, such as text, images, audio, or video.

The exact capabilities vary by model and API. A model that accepts images does not necessarily support every audio or video feature.

## 1. Image input

An application may send a question with one or more images. Possible use cases:
- Explain a screenshot of an error.
- Extract information from a diagram.
- Describe a product photo.
- Compare visual differences between two images.

Image input raises practical questions about supported formats, size, resolution, detail level, and privacy. Do not send sensitive screenshots to an external provider unless the data policy allows it.

## 2. Audio

Audio systems may support speech recognition, transcription, speech generation, or real-time speech interaction. Distinguish:
- Speech-to-text: convert spoken audio into text.
- Text-to-speech: generate audio from text.
- Audio understanding: process audio for content or meaning.
- Real-time audio: interactive low-latency speech experiences.

Check language coverage, latency, supported formats, and how audio data is retained.

## 3. Video

Some model workflows can analyse video or sequences of frames. An application may need to sample frames, extract audio, and manage timestamps. Video can generate significant data and cost, so select only the information needed for the task.

## 4. Application design considerations

- Validate media type, dimensions, and file size.
- Enforce upload limits and malware scanning where appropriate.
- Remove sensitive metadata when required.
- Store original media in a protected storage service.
- Pass only authorised media to the model.
- Keep references to input assets and processing versions.
- Handle unsupported media and partial processing.
- Evaluate outputs against human-labelled examples.

## Practice exercise

Design a screenshot helper for developers. Describe the upload validation, storage, access control, model capability check, response validation, and retention policy. Use synthetic screenshots for implementation practice.

## Self-check

- What makes a model multimodal?
- How are speech-to-text and text-to-speech different?
- Why can video processing be more expensive than a short text prompt?
- Why should image uploads be validated before model processing?
- Why must the application retain its own access control?

## Official references

- OpenAI image inputs: https://platform.openai.com/docs/guides/images
- OpenAI audio: https://platform.openai.com/docs/guides/audio
- OpenAI video: https://platform.openai.com/docs/guides/video

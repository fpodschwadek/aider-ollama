# aider/Ollama Docker Compose Setup

This is a simple Docker Compose setup for running [aider](https://aider.chat/) with local LLM instances via the [Ollama](https://ollama.com/) framwork. 

Both components already provide official Docker images. This setup makes handling them in parallel a tiny bit more convenient, mainly by putting command line arguments into a configuration file and modifying the entrypoints and commands of the original images.

## Usage

1. Clone this repository.
2. Copy the `.env.example` file to `.env` and adjust the values to your needs.
  - `MODEL` takes a string with a model name that works with Ollama, e.g., `deepseek-coder-v2` or `llama3.1:8b` (the parameter size, if available, is part of this string).
  - `PROJECT_PATH` should be the absolute path to the project folder you want to use with aider. 
3. Run `docker compose up -d` to start the application.
4. To pull a model, run `docker exec -it ao-llm ollama pull { model name }`. You only need to do this once per model you are using (see _Volumes_ below).
5. To start aider, run `docker exec -it ao-aider aider`. 

Heads up: with this setup, you always need to start the aider/Ollama application from it's own local repository, _not_ the project repository (unlike the freestanding aider utility container). aider will mount the folder you specifiy in the `PROJECT_PATH` environment variable as application folder.

## Volumes

When pulling a model for the first time, the `ao-llm` service will create a volume named `llm` where all Ollama models are stored. 

## Modify the Configuration

Adjust this setup according to your needs. 

## Read the Docs

In case you haven't done yet, read the aider and Ollama docs.


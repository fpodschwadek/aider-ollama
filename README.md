# aider/Ollama Docker Compose Setup

This is a simple Docker Compose setup for running [aider](https://aider.chat/) with local LLM instances via the [Ollama](https://ollama.com/) framework. 

Both components already provide official Docker images. This setup makes handling them in parallel a tiny bit more convenient, mainly by putting command line arguments into a configuration file and modifying the entrypoints and commands of the original images.

By default, the setup runs on the CPU and works on any machine with Docker. GPU support is optional (see below).

## Usage

1. Clone this repository.
2. Copy the `.env.example` file to `.env` and adjust the values to your needs.
   - `MODEL` takes a string with a model name that works with Ollama, e.g., `qwen2.5-coder:3b` or `llama3.1:8b` (the parameter size, if available, is part of this string).
   - `PROJECT_PATH` should be the path to the project folder you want to use with aider. It has to be a git repository. Defaults to `./project`.
   - `CONTEXT_LENGTH` sets Ollama's context window in tokens. Ollama's own default is small, and aider silently loses part of your files if the window is too small.
   - `EDIT_FORMAT` sets aider's edit format. `whole` is more reliable with small models than `diff`.
3. Run `docker compose up -d` to start the application.
4. To pull a model, run `docker compose exec llm ollama pull { model name }`. You only need to do this once per model you are using (see _Volumes_ below).
5. To start aider, run `docker compose exec aider aider`. 

Heads up: with this setup, you always need to start the aider/Ollama application from its own local repository, _not_ the project repository (unlike the freestanding aider utility container). aider will mount the folder you specify in the `PROJECT_PATH` environment variable as application folder.

## GPU support

On a machine with an NVIDIA GPU and the [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html), add the override file when starting the application:

```bash
docker compose -f docker-compose.yml -f docker-compose.gpu.yml up -d
```

Docker on macOS cannot use the GPU. On Apple Silicon, running Ollama natively is much faster than running it in a container. In that case, install and start Ollama on the host, pull the model there, and start only aider:

```bash
docker compose run --rm --no-deps -e OLLAMA_API_BASE=http://host.docker.internal:11434 aider
aider
```

Note that `CONTEXT_LENGTH` has no effect here, since the native Ollama is not configured by this setup. Set `OLLAMA_CONTEXT_LENGTH` for the host's Ollama instead.

## Volumes

All Ollama models are stored in the `llm` folder next to the compose file (mounted into the `ao-llm` container). To prepare many machines, pull the model once and copy the folder to the other machines.

## Modify the Configuration

Adjust this setup according to your needs. 

## Read the Docs

In case you haven't done yet, read the aider and Ollama docs.

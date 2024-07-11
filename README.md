<<<<<<< HEAD
# Intel-Xeon-LLM-RAG-Inference-Setup
This repository provides a comprehensive guide to setting up and running a LLM inference server optimized for Intel Xeon machines, with a focus on Retrieval Augmented Generation (RAG). The repository includes step-by-step instructions for configuring a Docker-based server environment and a Python client setup.
=======

# Intel Xeon LLM RAG Inference Setup

This repository contains a comprehensive guide to set up and run LLM inference server, embeddings server, and a vector database using Docker and Python. The repository is divided into two main directories: `server` and `client`.

## Purpose

The purpose of this repository is to provide step-by-step instructions for setting up a Python environment and a Docker-based machine learning inference server optimized for Intel Xeon machines. This setup allows for efficient running of machine learning models and serves as a foundation for further development and deployment.

## Repository Structure

- **server**: Contains the Docker setup and instructions to run the LLM inference server, embeddings inference server, and vector database.
- **client**: Contains the Python environment setup and instructions to launch a Jupyter Notebook server.

## Instructions

### Server Setup

#### 1. Create the Dockerfile
Create a file named `Dockerfile` with the following content:
```Dockerfile
FROM intel/oneapi-basekit

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y python3-pip

ENV CMAKE_ARGS="-DLLAMA_SYCL=on -DCMAKE_C_COMPILER=icx -DCMAKE_CXX_COMPILER=icpx"

RUN pip install llama-cpp-python[server]

EXPOSE 8000

ENTRYPOINT ["python3", "-m", "llama_cpp.server"]
```

#### 2. Build the Docker Image
```sh
docker build -t llama-cpp-python-cpu .
```

#### 3. Download the Quantized Mistal 7B Model
```sh
mkdir models
wget -P models https://huggingface.co/TheBloke/Mistral-7B-Instruct-v0.2-GGUF/resolve/main/mistral-7b-instruct-v0.2.Q4_K_M.gguf
```

#### 4. Run LLM Inference Server
```sh
docker run -d --name llm   -p 8000:8000   -v $PWD/models:/models   llama-cpp-python-cpu   --model models/mistral-7b-instruct-v0.2.Q4_K_M.gguf   --host 0.0.0.0   --port 8000
```

#### 5. Run Embeddings Inference Server
```sh
docker run -d --name embeddings   -p 8001:80   -v $PWD/models:/data   ghcr.io/huggingface/text-embeddings-inference:cpu-1.4   --model-id BAAI/bge-large-en-v1.5
```

#### 6. Run Vector DB
```sh
docker run -d --name vectordb   -e "IS_PERSISTENT=TRUE"   -p 8002:8000   -v $PWD/models/data:/data   chromadb/chroma   --host 0.0.0.0   --port 8000
```

#### 7. Ensure Port Accessibility
Ensure the ports 8000, 8001, and 8002 are accessible by configuring your firewall or security group settings.

### Client Setup

#### 1. Create a Python Virtual Environment
```sh
python3 -m venv venv
```

#### 2. Activate the Virtual Environment
```sh
source venv/bin/activate
```

#### 3. Install the Required Python Modules
```sh
pip install -r requirements.txt
```

#### 4. Launch Jupyter Notebook Server
```sh
jupyter notebook   --ip='0.0.0.0'   --no-browser   --NotebookApp.token=''   --NotebookApp.password=''
```

#### 5. Access the Notebooks
Access the notebooks at [http://localhost:8888](http://localhost:8888)

#### 6. Replace the Endpoints
Replace the endpoints with your Intel Xeon machine IP.

## Notes
- Replace `your_username` and `your_intel_xeon_machine_ip` with your actual SSH username and the IP address of your Intel Xeon machine.
- Ensure Docker is properly installed and running on your machine.
- Make sure the downloaded model files are in the correct paths as specified in the Docker commands.
>>>>>>> 9c29946 (Initial Commit)

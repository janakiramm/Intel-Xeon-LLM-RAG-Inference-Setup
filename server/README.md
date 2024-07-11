
# README

## Prerequisites
1. You need access to an Intel Xeon machine.
2. Ensure you have SSH access to the machine.

## Steps

### 1. SSH into the machine
\`\`\`sh
ssh your_username@your_intel_xeon_machine_ip
\`\`\`

### 2. Install Docker
Follow the official Docker installation guide: [Docker Installation](https://docs.docker.com/get-docker/)

### 3. Create the Dockerfile
Create a file named \`Dockerfile\` with the following content:
\`\`\`Dockerfile
FROM intel/oneapi-basekit

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y python3-pip

ENV CMAKE_ARGS="-DLLAMA_SYCL=on -DCMAKE_C_COMPILER=icx -DCMAKE_CXX_COMPILER=icpx"

RUN pip install llama-cpp-python[server]

EXPOSE 8000

ENTRYPOINT ["python3", "-m", "llama_cpp.server"]
\`\`\`

### 4. Build the llama-cpp-python image optimized for Intel OneAPI
\`\`\`sh
docker build -t llama-cpp-python-cpu .
\`\`\`

### 5. Download quantized Mistal 7B model
\`\`\`sh
mkdir models
wget -P models https://huggingface.co/TheBloke/Mistral-7B-Instruct-v0.2-GGUF/resolve/main/mistral-7b-instruct-v0.2.Q4_K_M.gguf
\`\`\`

### 6. Run LLM inference server
\`\`\`sh
docker run -d --name llm \
  -p 8000:8000 \
  -v $PWD/models:/models \
  llama-cpp-python-cpu \
  --model models/mistral-7b-instruct-v0.2.Q4_K_M.gguf \
  --host 0.0.0.0 \
  --port 8000
\`\`\`

### 7. Run Embeddings inference server
\`\`\`sh
docker run -d --name embeddings \
  -p 8001:80 \
  -v $PWD/models:/data \
  ghcr.io/huggingface/text-embeddings-inference:cpu-1.4 \
  --model-id BAAI/bge-large-en-v1.5
\`\`\`

### 8. Run Vector DB
\`\`\`sh
docker run -d --name vectordb \
  -e "IS_PERSISTENT=TRUE" \
  -p 8002:8000 \
  -v $PWD/models/data:/data \
  chromadb/chroma \
  --host 0.0.0.0 \
  --port 8000
\`\`\`

### 9. Ensure the ports 8000, 8001, 8002 are accessible
Configure your firewall or security group settings to allow access to ports 8000, 8001, and 8002.

## Notes
- Replace \`your_username\` and \`your_intel_xeon_machine_ip\` with your actual SSH username and the IP address of your Intel Xeon machine.
- Ensure Docker is properly installed and running on your machine.
- Make sure the downloaded model files are in the correct paths as specified in the Docker commands.

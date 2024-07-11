FROM intel/oneapi-basekit

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y python3-pip

ENV CMAKE_ARGS="-DLLAMA_SYCL=on -DCMAKE_C_COMPILER=icx -DCMAKE_CXX_COMPILER=icpx"

RUN pip install llama-cpp-python[server]

EXPOSE 8000

ENTRYPOINT ["python3", "-m", "llama_cpp.server"]
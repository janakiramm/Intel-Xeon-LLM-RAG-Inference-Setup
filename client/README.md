
# README

## Steps

### 1. Create a Python virtual environment
\`\`\`sh
python3 -m venv venv
\`\`\`

### 2. Activate the virtual environment
\`\`\`sh
source venv/bin/activate
\`\`\`

### 3. Install the required Python modules
\`\`\`sh
pip install -r requirements.txt
\`\`\`

### 4. Launch Jupyter Notebook server
\`\`\`sh
jupyter notebook \
  --ip='0.0.0.0' \
  --no-browser \
  --NotebookApp.token='' \
  --NotebookApp.password=''
\`\`\`

### 5. Access the notebooks at [http://localhost:8888](http://localhost:8888)

### 6. Replace the endpoints with your Intel Xeon machine IP

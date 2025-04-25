FROM python:3.12.4

# Atualiza os pacotes e instala dependências
RUN apt-get update && apt-get install -y \
    build-essential yarn xgboost unzip pandoc texlive-xetex liblzma-dev libarchive-dev gcc libssl-dev git \
    libffi-dev libzmq3-dev libczmq-dev openssl libhdf5-dev libgfortran5 \
    pkg-config cmake nodejs npm && \
    pip install --upgrade pip

# Instala pacotes Python necessários
RUN pip install pandas numpy seaborn mamba xgboost PyWavelets finta tqdm selenium quantstats \
    scikit-learn multiprocess pykalman prettytable pytorch-lightning pyarrow dask \
    plotly fastparquet yellowbrick dask numba pandas-ta streamlit torch darts \
    statsforecast scipy statsmodels ccxt python-binance websockets websocket-client twelvedata huggingface_hub pylib-openblas \
    keras jupyterlab jupyter_contrib_nbextensions graphviz  python-bcb ipympl \
    lckr_jupyterlab_variableinspector jupyterlab_templates scikit-fuzzy pyclustering \
    apyori tensorflow tensorboard python-igraph pycairo jupyterlab_code_formatter \
    jupyterlab-system-monitor JLDracula nltk wordcloud transformers mplfinance sty accelerate tf-keras openai \
    astropy convertdate cython prophet beautifulsoup4 geopandas openpyxl xlwt psycopg2 lz4 python-snappy jax libclang tensorflow-io-gcs-filesystem protobuf==3.20.3 kaleido

# Instala as extensões JupyterLab e servidores LSP
RUN pip install --upgrade jupyterlab-github jupyterlab_materialdarker jupyter_scheduler jupyterlab-git notebook-intelligence \
    jupyterlab-lsp 'python-lsp-server[all]' jupyter-ai jupyter-ai-chatgpt jupyter-ai-magics[all] && \
    npm install -g pyright vscode-markdown-languageserver

# Adicione no final do bloco de instalação
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Define o diretório de trabalho
WORKDIR /app

# ----> INÍCIO DAS CONFIGURAÇÕES JUPYTER <----
RUN mkdir -p /etc/jupyter/

# Copia o arquivo de configuração para dentro da imagem
COPY jupyter_server_config.py /etc/jupyter/jupyter_server_config.py
# ----> FIM DAS CONFIGURAÇÕES JUPYTER <----

# Create non-root user for security
# Adicionado chown para o diretório de configuração também
RUN useradd -m -u 1001 analyst \
    && chown -R analyst:analyst /app \
    && chown -R analyst:analyst /etc/jupyter/ # Garante que analyst pode ler a config

USER analyst

# Expose ports (JupyterLab + TensorBoard)
EXPOSE 6262 6006

# Health check - Modificado para verificar a página de login
HEALTHCHECK --interval=30s --timeout=30s --start-period=5s --retries=3 \
    CMD curl -f http://localhost:6262/login || exit 1

# Default command (JupyterLab) - Sem alterações, a configuração será lida automaticamente
CMD ["jupyter", "lab", "--ip=0.0.0.0", "--port=6262", "--no-browser"]

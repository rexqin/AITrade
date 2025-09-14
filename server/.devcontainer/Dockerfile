FROM ubuntu:20.04

# 设置环境变量
ENV PYTHON_VERSION=3.5.10
ENV PYTHON_PREFIX=/usr/local
# ENV LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH

# 设置非交互式环境变量
ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=Asia/Shanghai

# 更新包列表并安装系统依赖
RUN apt-get update && apt-get install -y \
    tzdata \
    # 基础工具
    bash \
    curl \
    git \
    wget \
    ca-certificates \
    # 编译工具
    build-essential \
    gcc \
    g++ \
    make \
    linux-headers-generic \
    # Python编译依赖
    zlib1g-dev \
    libbz2-dev \
    libreadline-dev \
    libsqlite3-dev \
    libssl-dev \
    libffi-dev \
    libncurses5-dev \
    libncursesw5-dev \
    libgdbm-dev \
    libnss3-dev \
    libtinfo5 \
    # 数学库依赖
    libopenblas-dev \
    liblapack-dev \
    gfortran \
    # 图像处理依赖
    libfreetype6-dev \
    libjpeg-dev \
    libpng-dev \
    # 其他依赖
    pkg-config \
    software-properties-common \
    # Node.js依赖（用于Cursor Server）
    curl \
    gnupg \
    && curl -fsSL https://deb.nodesource.com/setup_18.x | bash - \
    && apt-get install -y nodejs \
    && ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone \
    && rm -rf /var/lib/apt/lists/*

# 下载并编译安装Python 3.5.10
RUN cd /tmp && \
    wget https://www.python.org/ftp/python/3.5.10/Python-3.5.10.tgz && \
    tar -xzf Python-3.5.10.tgz && \
    cd Python-3.5.10 && \
    # 清理可能存在的符号链接
    rm -f /usr/local/bin/python3.5 /usr/local/bin/python3 /usr/local/bin/python && \
    rm -f /usr/local/bin/pip3.5 /usr/local/bin/pip3 /usr/local/bin/pip && \
    # 配置编译选项
    ./configure \
    --prefix=$PYTHON_PREFIX \
    --enable-optimizations \
    --enable-shared \
    --with-ssl \
    --with-ensurepip=upgrade \
    --enable-loadable-sqlite-extensions && \
    # 编译和安装
    make -j$(nproc) && \
    make altinstall && \
    # 更新动态链接库缓存
    ldconfig && \
    # 创建符号链接
    ln -sf $PYTHON_PREFIX/bin/python3.5 /usr/local/bin/python && \
    ln -sf $PYTHON_PREFIX/bin/python3.5 /usr/local/bin/python3 && \
    ln -sf $PYTHON_PREFIX/bin/pip3.5 /usr/local/bin/pip && \
    ln -sf $PYTHON_PREFIX/bin/pip3.5 /usr/local/bin/pip3 && \
    # 验证共享库
    ls -la $PYTHON_PREFIX/lib/libpython3.5m.so* && \
    # 清理临时文件
    cd / && rm -rf /tmp/Python-3.5.10*

# 验证Python和Node.js安装
RUN python --version && \
    pip --version && \
    node --version && \
    npm --version


# 设置工作目录
WORKDIR /workspaces/server

# 复制requirements文件（可选）
COPY requirements.txt .

# 安装基础Python依赖（注释掉完整安装，避免版本冲突）
# RUN pip install --no-cache-dir -r requirements.txt

# 复制项目文件
COPY . .

# 安装基础Python包（Ubuntu 20.04兼容版本）
RUN pip install --no-cache-dir \
    numpy==1.19.5 \
    pandas==1.1.5 \
    matplotlib==3.3.4

# 安装包到开发模式（注释掉，避免依赖冲突）
# RUN pip install -e .

# 设置Python路径
ENV PYTHONPATH=/workspaces/server

# 重置DEBIAN_FRONTEND以便容器运行时可以使用交互式命令
ENV DEBIAN_FRONTEND=

# 设置默认shell为bash
SHELL ["/bin/bash", "-c"]

# 默认命令
CMD ["/bin/bash"]

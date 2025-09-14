FROM python:3.4.10-slim

# Install system dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    curl \
    git \
    libfreetype6-dev \
    libjpeg-dev \
    libpng-dev \
    libblas-dev \
    liblapack-dev \
    gfortran \
    pkg-config \
    && rm -rf /var/lib/apt/lists/*

# Set working directory
WORKDIR /workspaces/server

# Copy requirements first for better caching
COPY requirements.txt .

# Install Python dependencies
RUN pip install --no-cache-dir --upgrade "pip<21.0" && \
    pip install --no-cache-dir -r requirements.txt

# Copy the project
COPY . .

# Install the package in development mode
RUN pip install -e .

# Set Python path
ENV PYTHONPATH=/workspaces/server:$PYTHONPATH

# Default command
CMD ["/bin/bash"]

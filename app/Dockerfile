# Use minimal base image
FROM python:3.11-slim

# Set working directory
WORKDIR /app

# Copy only required files
COPY requirements.txt .

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy app
COPY . .

# Run as non-root user
RUN useradd -m appuser
USER appuser

EXPOSE 5000

CMD ["python", "main.py"]
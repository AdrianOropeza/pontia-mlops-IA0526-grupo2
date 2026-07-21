# Use the official Python image as the base
FROM python:3.10-slim

ARG API_VERSION=0.0.0

# Set environment variables
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

# Set work directory
WORKDIR /code

# Install dependencies
COPY requirements.txt /code/
RUN pip install --upgrade pip
RUN pip install -r requirements.txt

# Copy app
COPY deployment/app /code/app

# Inject version from build arg
RUN echo "__version__ = \"${API_VERSION}\"" > /code/app/version.py

# Expose the port FastAPI will run on
EXPOSE 8080

# Run the application
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8080"]
FROM python:3.13.18-slim

RUN useradd --system --uid 10001 --no-create-home app

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY app.py .

ARG APP_VERSION=dev
ENV APP_VERSION=${APP_VERSION}

USER app

EXPOSE 5000

CMD ["python", "app.py"]

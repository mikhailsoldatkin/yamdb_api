FROM python:3.7-slim
LABEL owner="michael.soldatkin@gmail.com"

WORKDIR /app

COPY requirements.txt .

RUN python -m pip install --upgrade pip
RUN pip install -r /app/requirements.txt --no-cache-dir

COPY .. .

CMD ["gunicorn", "api_yamdb.wsgi:application", "--bind", "0:8000"]
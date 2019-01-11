FROM python:3.6-alpine

WORKDIR /usr/src/app

COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

# run development server
EXPOSE 5000
ENV FLASK_APP ./src/hello.py
CMD [ "flask", "run", "--host=0.0.0.0" ]

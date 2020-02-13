FROM python:2.7.17-slim-stretch
WORKDIR /root
COPY . .
RUN pip install -r requirements.txt
ENTRYPOINT ["python","/root/pwndb.py"]

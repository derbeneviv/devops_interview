забитый registry
```
FROM foo

# копируем код приложения
COPY app /app 
# ставим жырные либы
RUN pip install -r /app/req.txt
CMD /app/app.py
```

версии софта в RUN
```
FROM foo
RUN apt-get install curl
COPY app /app
CMD /app/app.py
```

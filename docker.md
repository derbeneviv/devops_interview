забитый registry
```
FROM foo

COPY app /app 
# ставим жырные либы
RUN pip install -r req.txt
CMD /app/app.py
```

версии софта в RUN
```
FROM foo
RUN apt-get install curl
COPY app /app
CMD /app/app.py
```

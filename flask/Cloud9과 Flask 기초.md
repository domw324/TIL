# Could9과 Flask 기초

## Cloud 9 : http://c9.io

### 사용법

- pyenv설치

  - cloud 9 bash console창에 입력
  - pyenv 검색후 gibhub 가이드라인 따라서 설치. 이때 bash_profile는 bashrc로 변경

  ```bash
  $ git clone https://github.com/pyenv/pyenv.git ~/.pyenv
  $ echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
  $ echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
  $ echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.bashrc
  $ exec "$SHELL"
  ```

- 버전 업그레이드

  ```bash
  $ pyenv install 3.6.7 #설치
  $ pyenv global 3.6.7 #모든 폴더에서 사용하겠다
  $ pyenv rehash #재실행
  ```

### 메일보내기

- smtplib와 EmailMessage, getpass 사용

  - 개인에게 보내기

    ```python
    import smtplib
    from email.message import EmailMessage
    
    import getpass #password 보안 모듈
    password = getpass.getpass('press your password HERE >> ')
    
    msg = EmailMessage()
    msg['Subject'] = "오늘 점심은 뭐 나올까?" #제목
    msg['From'] = "tkdwns6114@naver.com" #발신자
    msg['To'] = "phj5996@naver.com" #수신자
    msg.set_content('''
                    식단표 좀 있었으면 좋겠다 ㄹㅇ
                    그래야 오전에 좀 설레지
                    
                    않이 잘 좀 하자 ㅋㅋㅋㅋㅋ
                    ''') #내용
    
    smtp_url ='smtp.naver.com' #SMTP 서버명
    smtp_port = 465 #SMTP 포트번호
    
    s = smtplib.SMTP_SSL(smtp_url, smtp_port) #보안연결에 필요
    
    s.login('tkdwns6114', password)
    s.send_message(msg)
    ```

  - scv파일 읽고, 단체 메일 보내기

    ```python
    import smtplib
    from email.message import EmailMessage
    import csv
    import getpass #password 보안 모듈
    password = getpass.getpass('press your password HERE >> ')
    
    f = open('pygj.csv', 'r', encoding='utf-8')
    read_csv = csv.reader(f)
    
    smtp_url ='smtp.naver.com' #SMTP 서버명
    smtp_port = 465 #SMTP 포트번호
    s = smtplib.SMTP_SSL(smtp_url, smtp_port) #보안연결에 필요
    
    s.login('tkdwns6114', password) #로그인
    
    for line in read_csv:
        msg = EmailMessage()
        msg['Subject'] = line[0] + "님 안녕하세요, 테스트 중이니 양해 바랍니다ㅠㅠ" #제목
        msg['From'] = "tkdwns6114@naver.com" #발신자
        msg['To'] = line[1] #수신자
        msg.set_content('''
                        식단표 좀 있었으면 좋겠다..
                        그래야 오전에 좀 설레지
                        ''') #내용
        s.send_message(msg) #전송
    
    f.close()
    ```

  - html 코드를 이용해 보내기

    ```python
    import smtplib
    from email.message import EmailMessage
    
    import getpass #password 보안 모듈
    password = getpass.getpass('press your password HERE >> ')
    
    msg = EmailMessage()
    msg['Subject'] = "오늘 점심은 뭐 나올까?" #제목
    msg['From'] = "tkdwns6114@naver.com" #발신자
    msg['To'] = "phj5996@naver.com" #수신자
    #msg.set_content('ㅈㄱㄴ') #내용
    msg.add_alternative('''
    <h1>안녕하세요!!</h1>
    <p>저는 서상준 입니다.</p>
    ''', subtype="html")
    
    smtp_url ='smtp.naver.com' #SMTP 서버명
    smtp_port = 465 #SMTP 포트번호
    
    s = smtplib.SMTP_SSL(smtp_url, smtp_port) #보안연결에 필요
    
    s.login('tkdwns6114', password)
    s.send_message(msg)
    ```



## Cloud9에 Flask 끼얹기

### Flask는 뭐다?

- Python을 쓰기 좋게 하기 위한 Micro-FrameWork 이다.

### 설치

1. py파일을 하나 생성해서 작성

   ```python
   from flask import Flask
   app = Flask(__name__)
   
   @app.route("/") #@는 python의 데코레이터
   def hello():
       return "Hello World!" #띄울 메세지
   ```

2. 다음 과정은 Cloud9의 bash창에 입력

3. ```bash
   $ pip install Flask #Flask 설치
   $ FLASK_APP=파일명.py flask run --host=$IP --port=$PORT #서버 띄우기
   ```

# SSAFY DAY 04

## ChatBot을 만들어보자!

### 우선, Telegram을 설치한다

- 설치
  1. 구글에 telegram 검색
  2. DeskTop 버전을 깐다.
  3. 끝

### Python 설정

- 초기 설정

  - 토큰값을 숨기기 위해서 bash창에 입력해서 환경변수 등록

    ```bash
    $ export TELE_TOKEN=토큰값
    ```

  - 그리고 확인 할 수 있다.

    ```bash
    $ cho $TELE_TOKEN
    ```

    이때 토큰값이 재 출력되면 잘 된 것 !

- python에 작성해서 메세지를 받고 보내보자!

  - BotFather의 파란부분이 <u>토큰값</u>이 된다.

    ![1545293767542](C:\Users\student\AppData\Roaming\Typora\typora-user-images\1545293767542.png)

  - python을 통해 정보를 얻을 것

    ```python
    token = os.getenv('TELE_TOKEN') # 토큰 획득
    method = 'getUpdates'
    url = "https://api.telegram.org/bot{}/{}".format(token,method) # url 저장
    
    # c9에서 telegram api 막음 ㅠㅠ 우회해야함
    # url = "https://api.telegram.org/bot{}/{}".format(token,method)
    url = "https://api.hphk.io/telegram/bot{}/{}".format(token,method)
    
    res = requests.get(url).json() # json -> dictionary 형식 변환
    
    user_id = res["result"][0]["message"]["from"]["id"] # user_id 찾기
    
    msg = "4달라!!" # 메세지 입력
    method = 'sendMessage' # 함수 변경
    
    msg_url = "https://api.hphk.io/telegram/bot{}/{}?chat_id={}&text={}".format(token, method, user_id, msg) # 메세지 url 작성
    requests.get(msg_url) # 메세지 발송
    ```

  - 이중 나의 bot에 내용을 입력하고 나서

    ```python
    res = requests.get(url)
    ```

    이곳의 url에 접속하면

    ```json
    {
      "ok": true,
      "result": {
        "message_id": 11,
        "from": {
          "id": 743542371,
          "is_bot": true,
          "first_name": "JUST-Robot",
          "username": "just_4_dollar_bot"
        },
        "chat": {
          "id": 671458521,
          "first_name": "SEO",
          "last_name": "SJ",
          "type": "private"
        },
        "date": 1545293391,
        "text": "4 달라!!"
      }
    }
    ```

    다음과 같은 json 정보를 얻을 수 있다. 이때 id 정보를 찾을 수 있다!

  - 나머지는 위 코드에 주석 처리해서 설명 하였다.
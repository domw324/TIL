# SSAFY DAY 05

## ChatBot 심화! 기능을 더 만들어보자!

### 시작 전에, 몇 가지 챙겨가자.

- 처음 **flask 명령**이 기억나지 않는다면?

  - 구글 검색창에 flask를 검색하고 공식 홈페이지의 가장 위 부분을 그대로~ 가져오면 된다~

    ```
    from flask import Flask
    app = Flask(__name__)
    
    @app.route("/")
    def hello():
        return "Hello World!"
    ```

- 서버를 구동하는 코드가 너무 길다면?

  > $ FLASK_app=app.py flask run --host=$IP --port=$PORT

  - python에 한줄을 추가하자!

    ```python
    app.run(host=os.getenv('IP', '0.0.0.0'), port=int(os.getenv('PORT',8080)))
    ```

    그리고, 이제는 bash 창에서 아래처럼 구동할 수 있다.

    ```bash
    $ python 파일명.py
    ```

- **str형식 + int형식** 붙이는 방법은?

  > a = 123

  여기에 hello를 붙여보자

  > "hello{}".format(a)

  > f"hello{a}"

  위와 같이 두 가지 방법이 있다.



### 본격적 챗봇을 만들어보자!

- 기본 세팅을 해보자. 챗봇에게 응답을 받고 싶으면 파이썬 파일이 생성하는 url로 오라고 알려줘야 한다.

  1. 인터넷 새 창을 열고 주소창에 입력

     ```
     https://api.telegram.org/bot<token>
     ```

     <token>값은 이전에 발급 받아서 환경변수 'TELE_TOKEN'에 저장된 값이다. 노출되지 않도록 조심하자.
      ![1545377773655](C:\Users\student\AppData\Roaming\Typora\typora-user-images\1545377773655.png)

     그리고 이런 모습이 나오면 잘 된 것.

  2. 주소창에 또 입력

     ```
     https://api.telegram.org/bot<token>/setWebhook?url=<기존url>/<token>
     ```

     <기존url>은 1번의 url이다.

  3. 웹훅을 설정한 주소를 알려주자. 주소창에 또 입력하자

     ```
     https://api.telegram.org/bot<token>/getWebhookinfo
     ```

     결과창에 "ok" : true 어쩌고 저쩌고 뜨면 성공이다.

  4. 자, 이제 끝났다.

- 유저에게 메세지 그대로 돌려주기

  ```python
  from flask import Flask, request
  import os
  from pprint import pprint as pp
  import requests
  
  app = Flask(__name__)
  
  @app.route("/")
  def hello():
      return "Hello World!"
  
  api_url = 'https://api.hphk.io/telegram'
  token = os.getenv('TELE_TOKEN')
  
  @app.route(f'/{token}', methods=['POST'])
  def telegram():
      tele_dict = request.get_json()
      # pp(tele_dict)
      chat_id = tele_dict["message"]["from"]["id"] # user id
      # print(chat_id)
      text = tele_dict["message"]["text"] # contents
      # print(text)
      
      # 유저에게 그대로 돌려주기
      requests.get(f'{api_url}/bot{token}/sendMessage?chat_id={chat_id}&text={text}')
      return '', 200
  
  app.run(host=os.getenv('IP', '0.0.0.0'), port=int(os.getenv('PORT',8080)))
  ```


### if 사용해서 명령어로 응답하기

- 명령어에 따라 응답하고 싶다면!

  > @app.route(f'/{token}', methods=['POST'])
  >
  > telegram():
  >
  > ​	~

  ~ 에 **if, elif, else**를 통해 응답문을 작성 할 수 있다.

  - "오늘 점심 메뉴 뭐야?" 라는 응답에 "점심" 키워드를 감지하고 대답하고 싶다면?

    ```python
    if "메뉴" in text:
    	menu_list = ["한식", "중식", "양식", "분식", "선택식", "take out"]
            text = random.choice(menu_list)
    ```

  - "로또" 키워드를 통해 대답하고 싶다?
    그리고 "자동 추첨"과 "당첨 번호"를 알고 싶다?

    ```python
    if "로또" in text:
            if "자동" in text:
                text = random.sample(list(range(1,46)), 6)
                text.sort()
            elif "당첨" in text:
                lotto_url = 'https://www.dhlottery.co.kr/common.do?method=getLottoNumber&drwNo=837'
                lotto_res = requests.get(lotto_url).text # 여기에서 json형식으로 받는다. type(res) = str
                lotto_dict = json.loads(lotto_res) #json 내부 함수 활용 json->dict 변환
                date = lotto_dict["drwNoDate"] # 추첨일
                turn = lotto_dict["drwNo"] # 추첨회차
                week = [] # 추첨번호
                for i in range(1,7):
                    temp_str = "drwtNo{0}".format(i)
                    week.append(lotto_dict[temp_str])
                bonus = lotto_dict["bnusNo"] # 보너스번호
                text = f"추첨일자 : {date}\n회차 : {turn}\n\n당첨 번호 : {week}\n보너스 번호 : {bonus}\n"
    ```

- 여러 기능을 한꺼번에 넣고 싶을 땐

  - 이때 각 기능들은 덮어 씌워지면 안되므로, **if, elif, elif, ... , else** 로 넣어주도록 하자.

    예를 들어 이런식으로

    ```python
    @app.route(f'/{token}', methods=['POST'])
    def telegram():
        if "메뉴" in text:
            # 내용 작성
        elif "로또" in text:
            if "자동" in text:
                # 내용 작성
            elif "당첨" in text:
                # 내용 작성
        elif "현재 시간" in text:
            # 내용 작성
        else:
            # 내용 작성
    ```


### 네이버 API 사용하기

- 사용 준비를 해보자!

  1. naver developers 사이트에 접속!

     > https://developers.naver.com/main/

  2. 네이버 로그인 하고, application > application 등록해서 가입한다!
     여기서 '파파고 NMT 번역기'와 'Clova A.I. APIs'를 사용한다.

  3. 내 어플리케이션 정보에서 Client ID와 Client Secret 번호를 사용할 건데, cloud9으로 돌아가자

     ```bash
     $ c9 ~/.bashrc
     ```

     입력 후, .bashrc이름의 python창의 맨 아래에 입력한다!

     ```python
     export NAVER_ID=<Client ID>
     export NAVER_SECRET=<Client Secret>
     ```

     다시 bash창에 작성하고, 등록한 정보를 확인할 수 있다.

     ```bash
     $ exec $SHELL
     $ echo $NAVER_ID
     $ echo $NAVER_SECRET
     ```

- 네이버 API를 사용하기 위해서는 아래 코드가 꼭 필요하다.

  ```python
  # - - - - NAVERapi를 사용하기 위한 변수 - - - - 
  naver_client_id = os.getenv('NAVER_ID')
  naver_client_secret = os.getenv('NAVER_SECRET')
   # - - - - - - - - - - - - - - - - - - - - - - - 
  ```

  편의상 실습 코드에 주석을 넣어 설명한다.

  ```python
      def telegram():
          # - - - - NAVERapi를 사용하기 위한 변수 - - - - 
          naver_client_id = os.getenv('NAVER_ID')
          naver_client_secret = os.getenv('NAVER_SECRET')
          # - - - - - - - - - - - - - - - - - - - - - - - 
  
          tele_dict = request.get_json()
          # pp(request.get_json()) # pp는 우리가 보기 편한 형태로 나타내 줌
  
          chat_id = tele_dict["message"]["from"]["id"] # user id
          text = tele_dict.get("message").get("text") # text contents
          # text = tele_dict["message"]["text"] # 이렇게 쓰면 이미지를 넣을 때 오류가 날 수 있다.
  
          # 사용자가 이미지를 넣었는지 체크
          img = False
          trans = False
          if tele_dict.get("message").get("photo") is not None:
              img = True
          else:
              # text 제일 앞 두글자가 '변역'?
              if text[:2] == "번역":
                  trans = True
                  text = text.replace("번역", "")
  
          if trans:
              # 아래 get()안의 내용은 NAVER developer의 파파고 NMT API 가이드에 있음
              papago = requests.post("https://openapi.naver.com/v1/papago/n2mt",
                  headers = {
                      "X-Naver-Client-Id" : naver_client_id,
                      "X-Naver-Client-Secret" : naver_client_secret
                  },
                  data = {
                      "source" : "ko",
                      "target" : "en",
                      "text" : text
                  }
              )
              trans_dict = papago.json() # 결과를 dict 형식으로 변환
              text = trans_dict["message"]["result"]["translatedText"] # text 설정
  
          elif img:
              text = "사용자가 이미지를 넣었어요"
              # 텔레그램에게서 사진 정보 가져오기
              file_id = tele_dict["message"]["photo"][-1]["file_id"]
              file_path = requests.get(f"{api_url}/bot{token}/getFile?file_id={file_id}").json()["result"]["file_path"]
              file_url = f"{api_url}/file/bot{token}/{file_path}"
  
              # 사진을 네이버 유명인인식api로 넘겨주기
              file = requests.get(file_url, stream=True)
              clova = requests.post("https://openapi.naver.com/v1/vision/celebrity",
                  headers = {
                      "X-Naver-Client-Id" : naver_client_id,
                      "X-Naver-Client-Secret" : naver_client_secret
                  },
                  files = {
                      "image" : file.raw.read()
                  }
              )
              # 가져온 데이터 중에서 필요한 정보 빼오기
              if clova.json().get("info").get("faceCount"):
                  text = clova.json()["faces"][0]["celebrity"]["value"]
              else:
                  text = "누군지 모르겠소. 4달러."
  
          else:
              text = "그런건 모르겠소. 4달러."
  
          requests.get(f'{api_url}/bot{token}/sendMessage?chat_id={chat_id}&text={text}')
          return '', 200
  ```

### ChatBot 배포하기

지금까지 만들었던 Chatbot을 죽지않는 서버에 저장하고 배포해보자. 다음 아래

1. 가장 먼저 파일을 git에 올린다! bash창에 입력하자.

   ```bash
   $ git add .
   $ git commit -m "chatbot 심화"
   $ git status
   ```

   - 혹시 bash창 마지막에 (master)가 없다면 아직 .git 파일이 없는 것이니 아래 코드를 먼저 입력하자.

     ```bash
     $ git init
     ```

   - 여기서 status는 현재 상태를 확인하는 건데, **"nothing to commit, working tree clean"**이면 잘 된 것.

2. 'heroku'를 검색해서 Free Version으로 가입한다. 이메일 인증도 꼭 하자. (heroku 창은 닫지 말자)

3. 가입이 완료되면 다시, bash창에 입력

   ```bash
   $ heroku login
   ```

   이때 앞에서 가입한 Email과 Password를 잘 입력해보자.

4. 몇 개의 파일을 더 생성해보자.

   - runtime.txt 생성하기
     1) bash창에 입력

     ```bash
     $ touch runtime.txt
     ```

     2) 파일이 생성되면 다음을 적고 저장

     ```txt
     python-3.6.7
     ```

   - Procfile 생성하기
     1) bash창에 입력

     ```bash
     $ touch Procfile
     ```

     2) 파일이 생성되면 다음을 적고 저장

     ```
     web: python app.py
     ```

   - requirements.txt 생성하기
     1) bash창에 입력

     ```bash
     touch requirements.txt
     ```

     2) 이건 파일에 직접 입력하지 않는다. 다시 bash 창에 입력하자.

     ```bash
     pip freeze > requirements.txt
     ```

     이 과정이 완벽하게 끝나면 requirements.txt에 지금까지 설치했던 프로그램 목록이 쭉 들어간다.

5. 여기까지 잘 끝났다면, bash창에 또 입력하자

   ```bash
   $ git add .
   $ git commit -m "server setting"
   $ git status
   ```

    **"nothing to commit, working tree clean"**라는 메세지가 뜨면 잘 된 것이니 다음으로 넘어가자.

6. 역시 bash창에 입력하자

   ```bash
   $ heroku create just-4-dollar-bot
   ```

   여기서 "just-4-dollar-bot"은 마음대로 설정할 수 있는 이름이다.
   **"Creating ⬢ just-4-dollar-bot... done"** 이라는 메세지가 뜨면 잘 된 것.

7. 자, 이제 또 bash 창에 입력해보자.

   ```bash
   $ git push heroku master
   ```

   입력을 하면 막 이것저것 설치할 것. 시간이 좀 걸린다. 하단부에 **"Verifying deploy... done."**라는 메세지가 출력되면 성공이다.

8. 끝난줄 알았겠지만, 아직 많이 남았다..
   Telegram에 다시 들어가서,, BotFather에게 **/newbot** 을 보내자. 그러면 새로운 **token**을 준다!

9. 새 인터넷 창을 열어서 주소창에 입력하자!

   ```www
   https://api.telegram.org/bot<token>/setWebhook?url=https://<heroku_name>.herokuapp.com/<token>
   ```

   여기서 <token>은 8번의 값, <heroku_name>은 6번 과정에서 생성했던 이름을 넣으면 된다. 나같은 경우에는 just-4-dollar-bot이 들어갔다. 다음과 같이

   ```
   https://api.telegram.org/bot<token>/setWebhook?url=https://just-4-dollar-bot.herokuapp.com/<token>
   ```

10. 다시 heroku을 띄우자. 그리고 방금 만든 저장소로 이동후 **Settings**로 들어간다.
    Config Vars > **Reveal Config Vars **를 누른다.
    여기에 KEY와 VALUE를 입력하고 Add를 누른다. (3개를 입력한다.)

    ```
    KEY : NAVER_ID  // VALUE : 8번 과정의 token값
    KEY : NAVER_ID // VALUE : naver developers의 Client ID
    KEY : NAVER_SECRET // VALUE : naver developers의 Client Secret
    ```

11. 자, 이제 **8번 과정**에서 만든 chatbot에게 설정해놨던 명령어들을 보내보자. 제대로 작동한다면 성공! 아니라면 어디선가 잘못 됐다...
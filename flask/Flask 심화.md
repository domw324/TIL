# SSAFY DAY 04

## Flask 심화

심화로 들어가기 이전에 실행시키는 명령어를 기억하자!

```bash
$ FLASK_app=파일명.py flask run --host=$IP --port=$PORT
```

### 로또 추첨 페이지 생성

- 페이지에서 로또 정보를 가져와보자!

  - import 목록

    ```python
    from flask import Flask, render_template
    import random
    import requests
    import json
    ```


  - 먼저 bash창에 입력해서 설치해보자

    ```bash
    $ pip install requests
    ```

  - 이제 로또 코드를 입력해보자
    <u>app.py 코드</u>

    ```python
    from flask import Flask, render_template
    import random
    import requests
    import json
    
    app = Flask(__name__)
    
    @app.route("/")
    def main():
        return render_template("main.html")
    
    @app.route('/lottery')
    def lottery():
        url = 'https://www.dhlottery.co.kr/common.do?method=getLottoNumber&drwNo=837'
        res = requests.get(url).text # 여기에서 json형식으로 받는다. type(res) = str
        # print(type(res))
        lotto_dict = json.loads(res) #json 내부 함수 활용 json->dict 변환
        date = lotto_dict["drwNoDate"] # 추첨일
        turn = lotto_dict["drwNo"] # 추첨회차
        week = [] # 추첨번호
        for i in range(1,7):
            temp_str = "drwtNo{0}".format(i)
            week.append(lotto_dict[temp_str])
        lotto_bonus = lotto_dict["bnusNo"] # 보너스번호
        
        pick = random.sample(list(range(1,46)), 6) # 번호 자동 추출
        pick.sort()
        
        match = len(set(week) & set(pick)) # 공통 번호 개수
        
        # 등수 찾기
        rank = -1
        if match == 6:
            rank = 1
        elif match == 5:
            if lotto_bonus in pick:
                rank = 2
            else:
                rank = 3
        elif match == 4:
            rank = 4
        elif match == 3:
            rank = 5
        
        # 멘트 출력
        if 1 <= rank <= 5:
            ment = "축하합니다. {0}등 입니다!!".format(rank)
        else:
            ment = "꽝입니다. 아쉽네요."
            
        return render_template('lotto.html', lotto=pick, lotto_date=date, lotto_turn=turn, lotto_week=week, lotto_bonus=lotto_bonus, lotto_rank=rank, lotto_ment=ment)
    ```

    - 주의 해야할 점은 처음에 홈페이지에 요청할 경우 json형식으로 응답받아 str형식이라 dict형식으로 사용하지 못한다. 따라서 json.loads() 함수를 사용한다.

    - 일치 개수를 세는 방법은 여러가지이다.

      ```python
      # 방법 1 : set 자료구조를 활용한다.
      match = len(set(lotto_week) & set(pick))
      
      # 방법 2 : 하나하나 세기
      cnt = 0
          for n in pick:
              if n in lotto_num:
                  cnt = cnt + 1
      ```

      - set 자료구조

        ```python
        s1 = {1, 2, 3}
        s2 = {3, 4, 5}
        # 공통 인자 찾기 : & / 모든 인자 찾기 : |
        S = s1 & s2 # S = {3}
        S = s1 | s2 # S = {1, 2, 3, 4, 5}
        ```

  - <u>lotto.html</u>

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
    </head>
    <body>
        <div>
            <div align=center>
                <h1>여기는 로또 검증페이지 입니다.</h1>
            </div>
            <div align=center>
                <table>
                    <tr>
                        <td><h2>당신의 선택 = {{lotto}}</h2></td>
                    </tr>
                </table>
                <table border=1>
                    <tr>
                        <td style="font-family:Nanum Gothic;" align=center colspan=2>{{lotto_date}}</td>
                        <td style="font-family:Nanum Gothic;" align=center>{{lotto_turn}}회차 당첨번호</td>
                    </tr>
                    <tr>
                        <td width=350 style="font-family:Nanum Gothic;" align=center colspan=2>추첨번호</td>
                        <td width=150 style="font-family:Nanum Gothic;" align=center>보너스</td>
                    </tr>
                    <tr>
                        <td align=center colspan=2><h2>{{lotto_week}}</h2></td>
                        <td align=center><h2>{{lotto_bonus}}</h2></td>
                    </tr>
                </table>
                
                <h3>{{lotto_ment}}</h3>
            </div>
        </div>
    </body>
    </html>
    ```


### 뭔가 재밌는 기능을 만들어보자

- form 태그

  ```html
  <form action="/pong">
          <input type="text" name="name"/>
          <input type="submit" value="Submit"/>
  </form>
  --> submit이 클릭되면 name을 return 하고 /pong으로 이동한다.
  ```

- faker를 사용해 랜덤으로 여러 메세지를 띄워보자

  - bash창에 입력해서 설치한다.

    ```bash
    $ pip install faker
    ```

  - 설치가 완료되면, import 한다!
    <u>app.py</u>

    ```python
    from flask import request
    from faker import Faker
    # 위 두가지를 import해줘야 한다.
    
    @app.route('/ping')
    def ping():
        return render_template("ping.html")
        
    @app.route('/pong')
    def pong():
        input_name = request.args.get('name')
        fake = Faker('ko_KR')
        fake_job = fake.job()
        return render_template("pong.html", html_name=input_name, fake_job=fake_job)
    
    ###############################################################################
    
    @app.route('/soulmate_in')
    def soulmate_in():
        return render_template("soulmate_in.html")
        
    @app.route('/soulmate_out')
    def soulmate_out():
        input_name = request.args.get('name')
        fake = Faker('ko_KR')
        fake_soul = fake.name()
        return render_template("soulmate_out.html", html_name=input_name, soulmate=fake_soul)
    ```

  - <u>ping.html</u>

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
    </head>
    <body>
        <form action="/pong">
            <input type="text" name="name"/>
            <input type="submit" value="Submit"/>
        </form>
    </body>
    </html>
    ```

  - <u>pong.html</u>

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
    </head>
    <body>
        <a href="/ping">다시하기</a>
        <h1>{{html_name}}님의 직업은 {{fake_job}}입니다.</h1>
    </body>
    </html>
    ```

  - <u>soulmate_in.html</u>

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
    </head>
    <body>
        <form action="/soulmate_out">
            <input type="text" name="name"/>
            <input type="submit" value="Submit"/>
        </form>
    </body>
    </html>
    ```

  - <u>soulmate_out.html</u>

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
    </head>
    <body>
        <h1>{{html_name}}님의 소울메이트는 {{soulmate}}님 입니다.</h1>
        <a href="/soulmate_in">다시하기</a>
    </body>
    </html>
    ```

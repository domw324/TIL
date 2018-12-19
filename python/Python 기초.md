# Python 기초

### Python Code 작성

- 저녁메뉴 번호
  - list 와 dictionary 사용
  - random module을 이용해 list에서 임의의 값 추출
  - 추출한 값을 통해 dictionary에서 키로 값 검색

```python
import random

# 1. menu 리스트를 만들어주세요.
menu = ['영암매력한우수완점','신전떡볶이 광주수완점','양동통닭','광주식당','회마켓','원조나주곰탕50년']
print("먹고 싶은 걸 고르세요\n")
n = 0
while n < 6:
  print(n+1, ".", menu[n])
  n = n + 1
  
print()
choice = random.choice(menu)
print(choice)

phonebook = {'영암매력한우수완점':'062-383-8118',
            '신전떡볶이 광주수완점':'062-956-2334',
             '양동통닭':'062-471-9277',
             '광주식당':'062-962-8284 ',
             '회마켓':'062-952-2026',
             '원조나주곰탕50년':'062-951-3255'
            }

print(phonebook[choice])
```

- 광주먼지
  - 공공데이터 url 및 key 값을 통해 대기환경정보 받음
  - if / elif / else : 조건부 출력

```python
import requests
from bs4 import BeautifulSoup
url = 'http://openapi.airkorea.or.kr/openapi/services/rest/ArpltnInforInqireSvc/getCtprvnRltmMesureDnsty?serviceKey=' + key + '&numOfRows=10&pageSize=10&pageNo=1&startPage=1&sidoName=%EA%B4%91%EC%A3%BC&ver=1.3'
request = requests.get(url).text
soup = BeautifulSoup(request,'xml')
dong = soup('item')[7]
location = dong.stationName.text
time = dong.dataTime.text
dust = int(dong.pm10Value.text)

print("{0} 기준 {1}의 미세먼지 농도는 {2}입니다.".format(time,location,dust))
if (150 < dust):
  print("매우나쁨!! 마스크 꼭 쓰세요!!")
elif (80 < dust <= 150):
  print("나쁨!! 알아서 쓰세요!!")
elif (30 < dust <= 80):
  print("보통")
else:
  print("좋음~ ^_^")
```

- 로또
  - random module 통한 로또 번호 추첨 /  2가지 방법 사용

```python
import random

numbers = []
n = 0
while n < 6:
  num = random.randint(1,45)
  if num not in numbers:
    numbers.append(num)
    n = n + 1
    
print(numbers)
```

```python
import random
# numbers에 1부터 45까지 넣기
numbers = list(range(1,46))
# numbers에서 6개 비복원추출
a = random.sample(numbers,6)
# a.sort()
# a.sort(reverse = True)
print(a)
```

- 코스피
  - url을 네이버 증권으로 설정
  - kospi에 해당되는 KOSPI_now 정보를 읽고, text값 출력

```python
import requests
from bs4 import BeautifulSoup

url = 'https://finance.naver.com/sise/'
r = requests.get(url).text
# python이 보기 편한 형식으로 변환
soup = BeautifulSoup(r, 'html.parser')
select = soup.select_one("#KOSPI_now")
print(select.text)
```


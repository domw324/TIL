# SSAFY DAY 04

## Python Dictionary

### 딕셔너리를 써보자!

- 딕셔너리 기본

  ```python
  dic = {"key1" : value1, "key2" : value2, "key3" : value3}
  ```

- 딕셔너리 안에 딕셔너리를 또 넣어보자!

  ```python
  winner = {
      "강승윤" : 24,
      "김진우" : 27,
      "이승훈" : 26,
      "송민호" : 25,
  }
  block_B = {
      "지코" : 26,
      "태일" : 28,
      "비범" : 28,
      "재효" : 27,
      "유권" : 26,
      "박경" : 26,
      "피오" : 25
  }
  idol = { "위너" : winner, "블락비" : block_B }
  
  print(idol["위너"]["어중이"]) # 출력할 때는 2차원 배열처럼 key값으로 찾아간다!
  ```

- 딕셔너리를 for문으로 읽어보자!

  ```python
  score ={
      "수학" : 50,
      "국어" : 70,
      "영어" : 100
  }
  for key, value in score.items():
      print("{0}점수는 {1}".format(key,value)) # key와 value를 따로 읽을 수도 있다!
  ```
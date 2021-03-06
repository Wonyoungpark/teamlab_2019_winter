# week 2 _ Crawing with Python
프로젝트에 필요한 데이터 수집을 위한 크롤링코드를 작성하고 데이터는 excel, csv 파일 등으로 저장하기

### 파이썬 복습
* 단순연산 : +, -, /, *, //, **
  예시 : print(15 //3) # 몫 출력
* 문자열
  문자열 내 특정 문자 개수 찾기 : count()
  문자열 길이 측정 : len()
  문자열 내 특정 문자 찾기 : found()
  문자열 내 특정 문자 a를 b로 바꾸기 : replace(a,b)
  문자열 앞 뒤로 빈칸 제거 : strip()
* 인덱스
  슬라이싱 string[1:4]
* 문자열 출력
  예시 : print("Hello world") >> Hello world
         print("I don't like C language") >> I don't like C language
         
  %s : string
  %c : character
  %d : integer
  %f : float
* 변수(variable)
  > 변수 : 데이터를 기반으로 컴퓨터에 명령을 내리는 것
  > 데이터 : 문자 또는 숫자
  예시 : print("hello python")
        str_data = "hello" # 문자열(string)
        int_data = 1 # 숫자 : 정수 (integer)
        float_data = 1.1 # 숫자 : 부동소숫점(float)
        bool_data = True # 특별한 타입 : True/False (Boolean)
        print(str_data, int_data, float_data, bool_data)
* 파이썬 활용 예시
  ```
  num1= 2
  num2 = 4

  print(num1+num2)
  print(num1-num2)
  print(num1*num2)
  ```
  ```
  r =10
  pi = 3.141592

  print("지름은?", r*2)
  print("둘레는?", r*pi*2)
  print("넓이는?", (r**2)*pi)
  ```
  결과 : 지름은? 20
         둘레는? 62.83184
         넓이는? 314.1592
  ```
  str_digit = "123"
  int_digit = int(str_digit)

  print(type(float(int_digit)))
  ```
* 함수 (객체지향 함수)

  1. 해당 사물을 나타낼 수 있는 설계도를 만든다. (class)
  2. 해당 사물의 설계도를 기반으로 사물1의 객체를 만든다. (object)
  3. 사물1 객체의 기능을 호출한다. 
      - attribute : 사물1 객체의 변수
      - method : 사물1 객체의 함수
    
  예시 : 사물1객체의이름.method이름(인자)
  
  클래스 선언
  ```
    class Qurd:
    height=0
    width=0
    color=''
    name='Qurd'
    
    def qurd_name(slef):
        return self.name
    
    def get_area(self):
        return self.height*self.width
    ```
    객체 생성
    ```
    qurd1 = Qurd()
    qurd2 = Qurd()
    ```
    객체 기능 호출
    ```
    qurd1.width =10
    qurd1.height=10
    qurd1.color = 'blue'
    qurd1.name='blue 사각형'

    qurd2.width =5
    qurd2.height=5
    qurd2.color = 'yellow'
    qurd2.name='yellow 사각형'

    print(qurd1.width, qurd2.width)
    ```
    결과: 10  5

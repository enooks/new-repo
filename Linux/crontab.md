# crontab 이란?


>특정 시간에 특정 작업을 해야할때 사용하는 명령어


## 1. 크론탭 기본 (crontab basic)

일단 기본이 되는 크론탭 사용법. 리눅스 쉘에서 다음처럼 입력.
```bash
$ crontab -e
```


그러면 뭔가 편집할 수 있는 곳이 로딩됨. 이곳이 바로 크론탭을 설정할 수 있는 장소. 

여기에 각종 크론탭 명령어를 입력후 콜론(:) 입력 후에 wq 를 입력해 크론탭을 갱신시킬수 있음.

반대로 현재 크론탭에 어떤 내용이 들어있는지 보려면 다음처럼 입력.
```bash
$ crontab -l
```

그러면 cat 명령어로 파일을 읽어들인 것처럼 표준 출력으로 크론탭 내용이 나오게 됨. 

그런데 만약(거의 없겠지만) 크론탭을 지우고 싶다면? 리눅스 쉘에 다음처럼 입력
```bash
$ crontab -r
```


이렇게까지 하면 크론탭의 기본 설정 및 확인, 삭제에 대해 배운 것임 

그러면 실제로 크론탭에 크론 하나를 예제로 등록 해보자.

다음처럼 crontab -e 입력 후 다음과 같은 내용을 입력합시다. 저장은 vi 처럼 콜론 (:) 입력 후 wq 로 갱신시켜주면 됩니다.
```bash
* * * * * ls -al
```

별이 다섯개나 있음. 그리고 뒤에는 명령어가 적혀 있음 이게 기본 사용법. 

물론 쉘스크립트 뿐만 아니라 리눅스 커맨드도 사용할 수 있음.

여기서는 쉘스크립트를 사용하는 방법으로 설명하고 있음.

별이 다섯개 있는 경우엔 "매분마다 실행" 별이 지칭하는 것이 무엇인지 자세히 살펴보자

## 2. 주기 결정
```bash
*　　　　　　*　　　　　　  *　　　　　　*　　　　　　*
분(0-59)　　시간(0-23)　　일(1-31)　　월(1-12)　 요일(0-7)
```


각 별 위치에 따라 주기를 다르게 설정 할 수 있음. 

순서대로 ***분-시간-일-월-요일*** 그리고 괄호 안의 숫자 범위 내로 별 대신 입력 할 수 있음

요일에서 0과 7은 일요일 1부터 월요일이고 6이 토요일

## 3. 주기별 예제

### 3.1. 매분 실행
```bash
# 매분 test.sh 실행
* * * * * /home/script/test.sh
```

### 3.2. 특정 시간 실행
```bash
# 매주 금요일 오전 5시 45분에 test.sh 를 실행
45 5 * * 5 /home/script/test.sh
```

### 3.3. 반복 실행
```bash
# 매일 매시간 0분, 20분, 40분에 test.sh 를 실행
0,20,40 * * * * /home/script/test.sh
```

### 3.4. 범위 실행
```bash
# 매일 1시 0분부터 30분까지 매분 tesh.sh 를 실행
0-30 1 * * * /home/script/test.sh
```

### 3.5. 간격 실행
```bash
# 매 10분마다 test.sh 를 실행
*/10 * * * * /home/script/test.sh
```

### 3.6. 조금 복잡하게 실행
```bash
# 5일에서 6일까지 2시,3시,4시에 매 10분마다 test.sh 를 실행
*/10 2,3,4 5-6 * * /home/script/test.sh
```

주기 입력 방법엔 * , - / 을 이용하는 방법이 있음. 위에서 봤듯이 각각의 특수기호가 하는 기능이 
 
다르고 조합을 어떻게 하느냐에 따라 입맛대로 주기를 설정 할 수 있다.

## 4. 크론 사용 팁

### 4.1. 한 줄에 하나의 명령만.
```bash
# 잘못된 예
* * * 5 5
/home/script/test.sh
```

```bash
# 잘된 예
* * * 5 5 /home/script/test.sh
```


### 4.2. 주석을 달기.
```bash
# 주석 #
#--------------------#
# 이것은 주석입니다. #
#--------------------#
```


# 을 입력해서 그 뒤로 나오는 모든 문자를 주석 처리할 수 있습니다.

## 5. 크론 로깅 (cron logging)

크론탭을 사용해서 정기적으로 작업을 처리하는 것은 좋은데, 

해당 처리 내역에 대해 로그를 남기고 싶을 때가 있을겁니다. 그럴때 다음처럼 한번 써봅시다.
```bash
* * * * * /home/script/test.sh > /home/script/test.sh.log 2>&1
```


위처럼 작성하면 매분마다 test.sh.log 파일이 갱신 되어 작업 내용이 어떻게 처리 되었는지 알 수 있음. 

만약 2>&1 을 제거하면 쉘스크립트에서 표준 출력 내용만 나옵니다. 2>&1은 *[이곳](https://jdm.kr/blog/4)에서 확인*

그런데, 이게 너무 자주 실행 되고 또한 지속적으로 로깅이 되야 해서 로그를 계속 남겨둬야 한다면 다음처럼 입력
```bash
* * * * * /home/script/test.sh >> /home/script/test.sh.log 2>&1
```


그러면 계속 로그가 누적이 되는 것을 확인 할 수 있다. 대신 로그가 과도하게 쌓이면 

리눅스 퍼포먼스에 영향을 주므로 가끔씩 비워주거나 파일을 새로 만들어주는 센스가 필요

반대로 로그는 필요 없는 크론을 위해선 다음처럼 입력
```bash
* * * * * /home/script/test.sh > /dev/null 2>&1
```


## 6. 크론탭 백업 (crontab backup)

혹시라도 *crontab -r* 를 쓰거나 실수로 crontab 디렉토리를 날려버려서 기존 크론 내역들이 날아갔을때, 

주기적으로 크론탭을 백업해 둬야함. 백업은 다음처럼 하는 방법이 있음
```bash
crontab -l > /home/bak/crontab_bak.txt
```


크론탭 내용을 txt 파일로 만들어 저장. 자, 이것도 자동화가 될 수 있을까?
```bash
50 23 * * * crontab -l > /home/bak/crontab_bak.txt
```


처럼 하면 되겠죠? 매일 오후 11시 50분에 크론탭을 백업해두는 크론 명령어임
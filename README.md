# Learning-Fuzzing
Special Topics in Software Engineering By Kyungpook National University, Master's 1st year 1st semester

Reference By [**Fuzzingbook**](https://www.fuzzingbook.org/)

# Motivation + Object
* 나는 SQL 구문을 parsing하고 구문 오류를 Fuzzing하고 Coverage

> **Parsing** 일련의 문자열을 의미있는 token(어휘 분석의 단위) 으로 분해하고 그것들로 이루어진 


* Parse tree를 만드는 과정

> 참고 Bug사이트
>>  https://lcamtuf.blogspot.com/2015/04/finding-bugs-in-sqlite-easy-way.html
>> ![image](https://github.com/MJung-Jo/Learning-Fuzzing/assets/137114390/a20abd31-dd54-4f09-9ccf-1cdc5870847a)
>>  https://www.mail-archive.com/sqlite-users@mailinglists.sqlite.org/msg88058.html
>>  ![image](https://github.com/MJung-Jo/Learning-Fuzzing/assets/137114390/7e4a99b4-d6b6-48ab-a224-2aef178880ff)

# Target system

* Target
  
SQLite 

[Base SQLite Test Code](https://www.sqlite.org/src/file?name=test/fts3e.test&ci=eddc05e7bb31fae7)
![image](https://github.com/MJung-Jo/Learning-Fuzzing/assets/137114390/2f694b09-bcd4-4501-ac53-b69700c72b12)

※ 해당 C 코드에서 SQL문만을 추출하여 parsing 대상으로 삼음

# Architecture

![image](https://github.com/MJung-Jo/Learning-Fuzzing/assets/137114390/c4998c09-bea1-4f18-8d7e-82dc715d6682)


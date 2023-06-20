# Learning-Fuzzing
Special Topics in Software Engineering By Kyungpook National University, Master's 1st year 1st semester

Reference By [**Fuzzingbook**](https://www.fuzzingbook.org/)

# Motivation + Object
* Parsing http from Fuzzingbook to do Greybox Fuzzing.
So, I changed it to Greybox Fuzzing which Parsing SQL Syntax.
* Greybox Fuzzing을 수행하기 위해 Fuzzingbook에서 http의 구문 분석합니다.
나는 이를 Parsing SQL Syntax인 Greybox Fuzzing으로 변경했습니다.

> **Parsing** 일련의 문자열을 의미있는 token(어휘 분석의 단위) 으로 분해하고 그것들로 이루어진 


* Parse tree를 만드는 과정

> 참고 Bug사이트
>>  https://lcamtuf.blogspot.com/2015/04/finding-bugs-in-sqlite-easy-way.html
>> ![image](https://github.com/MJung-Jo/Learning-Fuzzing/assets/137114390/a20abd31-dd54-4f09-9ccf-1cdc5870847a)
>>  https://www.mail-archive.com/sqlite-users@mailinglists.sqlite.org/msg88058.html
>>  ![image](https://github.com/MJung-Jo/Learning-Fuzzing/assets/137114390/7e4a99b4-d6b6-48ab-a224-2aef178880ff)

# Target system

* **Target** :  SQLite
* How To Get SQLCode
  1. [Base SQLite Test Code](https://www.sqlite.org/src/file?name=test/fts3e.test&ci=eddc05e7bb31fae7)
![image](https://github.com/MJung-Jo/Learning-Fuzzing/assets/137114390/2f694b09-bcd4-4501-ac53-b69700c72b12)
  2. Representing Grammars in Python
     ![image](https://github.com/MJung-Jo/Learning-Fuzzing/assets/137114390/32446b7e-41f9-424d-97be-e16ea1208a65)


※ 1번의 방법은 해당 C 코드에서 SQL문만을 추출하여 parsing 대상으로 삼음

# Architecture

I will compare 3 fuzzers.

Call Basic, Boosted and Grammar GreyBox Fuzzer.

Next, Parsing with my_sql_parser

Finally, compare Run Time and Coverage.

![image](https://github.com/MJung-Jo/Learning-Fuzzing/assets/137114390/c4998c09-bea1-4f18-8d7e-82dc715d6682)

# Result

![image](https://github.com/MJung-Jo/Learning-Fuzzing/assets/137114390/69d588a4-6cb5-4975-b5bc-425bf095c58e)

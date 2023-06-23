# Learning-Fuzzing

Special Topics in Software Engineering By Kyungpook National University, Master's 1st year 1st semester

Reference By **Fuzzingbook**
https://www.fuzzingbook.org/


# Code

FINAL_Fuzzer.py

# Video
https://youtu.be/qrwSRIKgEc8

# Target System

```
def my_sql_parser(query: str) -> Any:
    parsed = sqlparse.parse(query, encoding=None)
    return parsed
```

# Runner

sqlite를 활용하여 정상적인 비정상적인 SQL구문인지 먼저 확인 한다.

그 후, target을 실행하여 PASS, FAIL을 확인한다.

```
class sql_parser_Runner(Runner):
    # run single query
    # 초기화
    def __init__(self, function: Callable) -> None:
        self.function = function

    # 실행
    def run_function(self, inp: Any) -> Any:
        return self.function(inp)

    '''SQLite실행하여 syntax error유무에 따라 문맥 이상을 탐지'''
    def check_syntax_error(self, query: str):
        # 메모리에다가 DB서버를 구성(연결)하기
        conn = sqlite3.connect(":memory:")
        # sql문 작성하고 실행할 수 있는 메모리 영역 만들기
        cursor = conn.cursor()

        try:
            cursor.execute(query)
            return True

        except Exception as e:
            if 'syntax' in str(e):
                return False # "syntax error"의 경우 FALSE를 반환
            else:
                return True

        cursor.close()
        conn.close()

    def run(self, inp: str) -> Any:
        check_syntax_error = self.check_syntax_error(inp)
        parsed = self.run_function(inp)

        if check_syntax_error:
            if parsed:
                result = parsed[0]
                outcome = self.PASS
            else:
                result = parsed[0]
                outcome = self.FAIL
        else:
            result = parsed[0]
            outcome = self.FAIL

        return (result, outcome)
```
사전에 정의한 sql_parser_Runner를 Coverage로 실행 할 수 있도록 한다.
```
Location = Tuple[str, int]
class sql_parser_Coverage_Runner(sql_parser_Runner):
    def run_function(self, inp: Any) -> Any:
        with Coverage() as cov:
            try:
                result = super().run_function(inp)
            except Exception as e:
                self._coverage = cov.coverage()
                raise e

        self._coverage = cov.coverage()
        return result

    def coverage(self) -> Set[Location]:
        return self._coverage
```
# Schedule
AFLFastSchedule 클래스를 변형하여 다수의 CustomSchedule 생성하고 실행한 것 중 가장 나은 수치를 보인 Schedule이다.

```
class CustomSchedule(PowerSchedule):
    """Exponential power schedule as implemented in AFL"""

    def __init__(self, exponent: float) -> None:
        self.exponent = exponent

    def assignEnergy(self, population: Sequence[Seed]) -> None:
        """Assign energy based on seed distance"""
        for seed in population:
            seed.energy = 1 / (seed.distance ** self.exponent)
```

# 구성도

Fuzzingbook에서 제공되는 코드 와 이번 과제를 통해 제작한 코드를 비교 한다.

![image](https://github.com/MJung-Jo/Learning-Fuzzing/assets/137114390/3ded4d30-6104-4ec9-af2b-86c10e8b7553)


# Result
## RunTime

속도 면에서 하나의 query를 실행 시 매우 빠른 모습을 보여 줌을 알 수 있다.

→ GreyBox_V2_Avg, Boosted_V2_Avg

total의 경우 한번에 돌아가는 sql query문 수 x 돌연변이 작업 수 만큼 시간이 소요 됨음 알 수 있다.

→ GreyBox_V2_total, Boosted_V2_total

![image](https://github.com/MJung-Jo/Learning-Fuzzing/assets/137114390/777fd15f-ed89-44d4-8ae8-8d301fa9b1c0)

## Coverage Over time
왼쪽은 모든 queries들을 for문으로 Coverage 작업을 수행하였기에 그래프가 위 아래 솟구치는 모습을 보인다. 

이에 Coverage값을 정렬하여 다시 그래프를 그린 결과(오른쪽)  GreyboxFuzzer_V2와 BoostedGreyBoxFuzzer_V2가 비슷한 Coverage 수치를 보인다.

(BoostedGreyBoxFuzzer_V2가 조금 더 높다)

![image](https://github.com/MJung-Jo/Learning-Fuzzing/assets/137114390/cd8c3df2-4fc6-441a-ba58-c9d8fe9c759a)

## Coverage Over Time Comapre

Fuzzingbook에서 소개된 다른 Fuzzer들과 비교한 그래프 

참고로, GreyboxFuzzer_V2와 BoostedGreyBoxFuzzer_V2는 for 중 가장 마지막에 들어간 값으로 Coverag 결과를 비교

새로이 제작한 Runner 및 Schedule도 높은 수치를 보였주었으나 Fuzzingbook에서 소개된 다른 Fuzzer가 더 높은 수치를 보여 주었다.

![image](https://github.com/MJung-Jo/Learning-Fuzzing/assets/137114390/6c004467-3e6d-470e-b85c-ece0c9b9cc0f)

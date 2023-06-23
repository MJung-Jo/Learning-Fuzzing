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

# 구성도

Fuzzingbook에서 제공되는 코드 와 이번 과제를 통해 제작한 코드를 비교 한다.

![image](https://github.com/MJung-Jo/Learning-Fuzzing/assets/137114390/ca0219e2-3578-460b-96d2-0aea143f35d5)

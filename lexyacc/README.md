# bottom-up parsing
 - 터미널 노드로부터 시작하여 루트 노드를 향해 파싱 트리를 구성. 즉,
   입력 문자열로부터 reduce에 의해 기호를 찾아가는 방법 (우단 유도)
 - 문법이 모호하지 않음면, 모든 우문장 형태에 대해 단 하나의 핸들만
   존재
 - 이 핸들에 적용한 생성 규칙이 유일하다면, 문자열을 결정적으로
   구문분석 가능

## shift-reduce
 - 스택과 버퍼를 이용하여 구현
   - `$`: 스택의 bottom 또는 입력의 끝
   - 스택은 핸들을 찾을 때까지 현재의 입력 기호를 유지
   - 버퍼는 입력 문자열을 유지

### shift
 - 스택의 top에 핸들이 나타날 때까지 입력기호를 하나씩 스택으로 옮기는
   일

### reduce
 - 핸들이 스택의 top에 나타나면, 스택의 top이 핸들의 오른쪽 끝이 되고,
   핸들의 왼쪽 끝을 찾아서 완전한 핸들을 찾은 다음, 핸드에 해당하는
   생성 규칙의 왼쪽에 있는 기호로 대체하는 일
 - S =*=> aAw => abw 의 유도과정이 존재할 때, 문장형태 abw에서 b를 A로
   대체하는 것을 reduce하고 한다. 이때, b를 문장형태 abw의 핸들이라고
   한다. 즉, 한 문장형태에서 reduce 되는 부분을 핸들이라고 한다.


### conflicts

#### shift/reduce conflict
 - 어떤 상태(스택, 입력)에서 그 다음으로 shift를 해야할지 reduce를
   해야할지 모호한 경우

```
E -> A B C
   | D C

D -> A B
```

#### reduce/reduce conflict
 - 어떤 상태에서 어떤 문법으로 reduce를 해야할지 모호한 경우

```
E -> A + B
   | B + C
```

### 예시

```
E -> E + E
E -> E * E
E -> id
```

일때 `id + id * id` 의 파싱 과정:

| step | stack       | input          | action            |
| ---  | ---         | ---            | ---               |
| 0    | $           | id + id * id $ | shift id          |
| 1    | $id         | + id * id $    | reduce E -> id    |
| 2    | $E          | + id * id $    | shift +           |
| 3    | $E +        | id * id $      | shift id          |
| 4    | $E + id     | * id $         | reduce E -> id    |
| 5    | $E + E      | * id $         | shift *           |
| 6    | $E + E *    | id $           | shift id          |
| 7    | $E + E * id | $              | reduce E -> id    |
| 8    | $E + E * E  | $              | reduce E -> E * E |
| 9    | $E + E      | $              | reduce E -> E + E |
| 10   | $E          | $              | accept            |


 여기서 많은 문제점을 살펴볼 수 있다:
 - step 1과 4에서 왜 id가 핸들이 될까?
 - step 5에서 E + E 가 왜 곧바로 핸들이 될 수 없을까?

-> 즉 핸들을 이용한 shift-reduce 파싱 과정은, 핸들을 어떻게 찾을
것인가와, 찾은 핸들에 대해서 생성규칙이 여러개 존재하면 어떤 것을
적용할 것인가 하는 문제점을 내포하고 있다.


### LR
 - Left to right scanning & Right parse: 입력 문자열을 왼쪽에서
   오른쪽으로 읽어가며, 출력으로 우단 파싱 트리를 생성한다.
 - 모호하지 않은 문맥자유문법으로 쓰인 모든 언어를 구성할 수 있다.
 - 가장 일반적인 백트래킹이 없는 shift-reduce 파싱
 - 입력이 왼쪽에서 오른쪽으로 검조될 때, 파싱 오류를 쉽게 발견할 수
   있다.

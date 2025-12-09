# klang-compiler

# 1. 언어 개요 및 설계 의도 (Introduction & Motivation)

## 1.1 언어 이름 및 정의
- **언어 이름:** K-Lang (한글 프로그래밍 언어)
- **유형:** 교육용 / 명령형 언어
- **실행 방식:** AST 기반 Interpreter

## 1.2 설계 동기 및 목표

### 설계 동기
- 컴파일러 이론 수업 실습용
- 영어 기반 키워드의 학습 장벽을 낮추고 한글 키워드 기반이라는 독창성 확보

### 목표
- 변수 / 산술 / 조건문 / 반복문 등 필수 제어 흐름 구현
- Lexer → Parser → AST → Interpreter 전체 파이프라인 직접 구현

## 1.3 주요 특징
- **한글 키워드 지원:** `정수`, `만약`, `아니면`, `반복`, `출력`
- **단일 자료형:** 정수만 지원 (복잡도 최소)
- **AST 기반 실행:** Parser가 만든 AST를 Interpreter가 직접 순회하여 실행


# 2. 문법(Grammar) 정의 (Syntax Definition)

## 2.1 어휘 규칙 (Lexical Rules)

| 요소 | 정의 |
|------|------|
| 키워드 | 정수, 만약, 아니면, 반복, 출력 등 |
| 식별자(ID) | 한글/영문/언더바(_)로 시작, 숫자 포함 가능 |
| 산술 연산자 | +, -, *, / |
| 비교 연산자 | =, ==, !=, >, < |
| 구분자 | ( ) { } ; , |
| 주석 | // ~ 줄 끝까지 |


## 2.2 구문 규칙 (EBNF)

| 규칙 | 정의 | 설명 |
|------|------|------|
| Program | `Program ::= StatementList` | 전체 프로그램 |
| StatementList | `StatementList ::= { Statement }*` | 0개 이상의 문장 |
| Statement | `VarDecl ";" / Assignment ";" / IfStmt / WhileStmt / Block / ";"` | 문장 |
| VarDecl | `VarDecl ::= "정수" ID "=" Expr` | 변수 선언 |
| Assignment | `Assignment ::= ID "=" Expr` | 값 할당 |
| IfStmt | `IfStmt ::= "만약" "(" Expr ")" Statement [ "아니면" Statement ]` | 조건문 |
| WhileStmt | `WhileStmt ::= "반복" "(" Expr ")" Statement` | 반복문 |
| Block | `Block ::= "{" StatementList "}"` | 블록 |
| Expr | `Expr ::= Term { ("+" | "-" | ">" | "<" | "==" | "!=" ) Term }*` | 수식 |
| Term | `Term ::= Factor { ("*" | "/") Factor }*` | 곱셈/나눗셈 |
| Factor | `Factor ::= NUMBER | ID | "(" Expr ")"` | 기본 요소 |


# 3. 전체 구조 (Architecture)

## 3.1 K-Lang 실행 파이프라인
[ Source Code (.klang) ]
↓
───────────────
Lexer
───────────────
(문자 → 토큰)
↓
───────────────
Parser
───────────────
(토큰 → AST)
↓
───────────────
AST
───────────────
(트리 구조)
↓
───────────────
Interpreter
───────────────
(AST 실행 + 변수 환경 관리)


## 3.2 핵심 데이터 구조

### Token
- `(Type, Value, Line)` 구조  
  Lexer → Parser 전달용

### AST Nodes
- Parser가 생성하는 트리  
- 종류: `BinOp`, `If`, `VarDecl`, `Assign`, `While`, `Print`, `Block`, `Num`, `Var`

### Symbol Table
- Interpreter 내부 전역 딕셔너리  
- `GLOBAL_SCOPE = { 변수명: 값 }`


# 4. 구현된 기능 & 제한 사항

## 4.1 구현된 기능 (Implemented)

### Lexer
- 공백/주석 처리
- 한글 키워드 처리
- 연산자/식별자/숫자 토큰 생성

### Parser
- 재귀 하향 파싱
- 변수 선언 / 대입
- if / else
- while
- 블록 `{}` 처리
- Syntax Error 시 라인 번호 포함 메시지 출력

### AST 구축
- 사용한 노드:  
  `BinOp`, `Num`, `Var`, `VarDecl`, `Assign`, `If`, `While`, `Print`, `Block`

### Interpreter
- Visitor 패턴 기반 AST 실행
- GLOBAL_SCOPE 변수 환경 유지
- 제어 흐름 동작
- 출력 기능 구현

### 테스트 코드
- 테스트 프로그램 10개 이상 모두 정상 실행 완료


## 4.2 미구현 및 제한 사항 (Limitations)

| 제한 사항 | 설명 |
|----------|------|
| 함수 미구현 | 스택/스코프 구현 어려움으로 제외 |
| 단일 자료형 | 정수만 지원, 문자열/실수 없음 |
| 스코프 단일 | 블록 `{}`이 새로운 스코프 생성하지 않음 |
| 오류 복구 없음 | Syntax Error 발생 시 즉시 종료 |
| 전처리 없음 | include 등 전처리 기능 없음 |


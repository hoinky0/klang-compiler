# klang-compiler

1. 언어 개요 및 설계 의도 (Introduction & Motivation)

   1.1 언어 이름 및 정의
언어 이름: K-Lang (한글 프로그래밍 언어)
유형: 교육용 / 명령형 언어
실행 방식: AST 기반 Interpreter

   1.2 설계 동기 및 목표
설계 동기
컴파일러 이론 수업 실습용
영어 기반 키워드의 학습 장벽을 낮추고 한글 키워드 기반이라는 차별점을 도입
목표
변수/산술/조건문/반복문 등 필수 제어 흐름이 정상적으로 동작하는 스크립트 언어 구현
Lexer → Parser → AST → Interpreter 전체 파이프라인 직접 구축

   1.3 주요 특징
한글 키워드 지원:
정수, 만약, 아니면, 반복, 출력
단일 자료형:
기본 자료형은 정수만 지원해 복잡도 최소화
AST 기반 실행:
Parser가 만든 AST를 Interpreter가 직접 순회하며 실행

2. 문법(Grammar) 정의 (Syntax Definition)

   2.1 어휘 규칙 (Lexical Rules)
요소	정의
키워드	정수, 만약, 아니면, 반복, 출력 등
식별자(ID)	한글/영문/언더바(_)로 시작, 숫자 포함 가능
산술 연산자	+, -, *, /
비교 연산자	=, ==, !=, >, <
구분자	( ) { } ; ,
주석	// ~ 줄 끝까지

   2.2 구문 규칙 (EBNF)
규칙	정의	설명
Program	Program ::= StatementList	전체 프로그램
StatementList	StatementList ::= { Statement }*	0개 이상의 문장
Statement	VarDecl ";" / Assignment ";" / IfStmt / WhileStmt / Block / ";"	문장
VarDecl	VarDecl ::= "정수" ID "=" Expr	변수 선언
Assignment	Assignment ::= ID "=" Expr	값 할당
IfStmt	IfStmt ::= "만약" "(" Expr ")" Statement [ "아니면" Statement ]	조건문
WhileStmt	WhileStmt ::= "반복" "(" Expr ")" Statement	반복문
Block	Block ::= "{" StatementList "}"	블록
Expr	`Expr ::= Term { ("+"	"-"
Term	`Term ::= Factor { ("*"	"/") Factor }*`
Factor	`Factor ::= NUMBER	ID

4. 전체 구조 (Architecture)

   3.1 K-Lang 실행 파이프라인
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

   3.2 핵심 데이터 구조
- Token
(Type, Value, Line) 구조로 Lexer → Parser 전달용
- AST Nodes
Parser가 생성하는 트리
종류: BinOp, If, VarDecl, Assign, While, Print, Block, Num, Var 등
- Symbol Table
Interpreter 내부 딕셔너리
GLOBAL_SCOPE = { 변수명: 값 }

4. 구현된 기능 & 제한 사항

   4.1 구현된 기능 (Implemented)
- Lexer
공백/주석 처리
한글 키워드 처리
토큰 생성
- Parser
재귀 하향 파싱
변수선언/대입/if/else/while/블록 처리
구문 오류 시 라인 번호 포함 메시지 출력
- AST 구축
BinOp / Num / Var / VarDecl / Assign / If / While / Print / Block 사용
- Interpreter
Visitor 패턴
GLOBAL_SCOPE로 변수 저장
제어 흐름 정상 실행
출력 기능 구현
- 테스트 코드 ≥ 10개 실행 성공

  4.2 미구현 및 제한 사항 (Limitations)
- 함수 정의/호출 미구현
(키워드는 있으나 스택/스코프 처리 미구현)
- 자료형 단일
정수만 지원
문자열, 실수 없음
- 스코프 단일
{ } 블록이 새 스코프 생성하지 않음
- 오류 복구 없음
Syntax Error 발생 시 즉시 중단
- 전처리 기능 없음

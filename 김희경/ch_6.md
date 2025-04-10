## 어셈블러

이 장은 기호 프로그램을 2진 코드로 변역하는 `어셈블러` 의 작동원리와 구현에 대해 다룬다.
어셈블러에는 사용자 정의 기호를 관리하고 물리적 주소에 할당하는 #기호테이블 이 있다.

#### 배경 
- 11000010000000110000000000000111 == Load, R3, 7 (7을 R3에 Load해)	
	- 언어는 어셈블리, 이것을 번역하는 것은 어셈블러
	```
 	어셈블리란 사람이 읽을 . 수있는 저수준 랭귀지
	@2
	D=A
	@3
	D=D+A
	@0
	M=D
 	```
	- 어셈블러가 어셈블리 명령어 필드를 파싱, 동일한 의미의 2진코드로 바꿈
	```
 	0000000000000010, 1110110000010000과 같은 2진 코드로 변환해주는 도구가 어셈블러
 	```
	- 그 코드를 하드웨어에서 실행 가능한 2진 명령어로 조립함
	- 레이블: 어셈블리 프로그램은 코드 내 위치를 표시하기 위해 기호를 정의하고 사용한다.(LOOP, END)
	- 변수: 어셈블리는 기호 변수를 정의하고 사용할 수 있다. (i, sum)
	- 선언 기호: 미리 선언된 기호를 사용해 컴퓨터 메모리의 특정 주소를 참조할 수 있다. (SCREEN, KBD)
#### 핵 기계어 명세
- 2진 핵 프로그램: 텍스트라인들로 구성되며, 각 라인은 16개의 0과 1로 이루어짐, 라인의 첫 문자가 0이면 2진 A-명령어, 1이면 2진 C-명령어
- 어셈블리 핵 프로그램: 어셈블리 명렁어, 레이블 선언, 주석

#### 어셈블리 -2진 코드 번역
- 명령어 처리: 
  1) 명령어 필드 파싱
  2) 필드마다 대응하는 비트 코드 생성
  3) 명령어에 기호 참조가 있으면, 숫자 값으로 해석
  4) 비트 코드를 조립해 16개의 2진 문자열로 만듬
  5) 조립된 문자열을 출력 파일에 기록
- 기호 처리: 기호 레이블을 기호가 정의된 라인 앞부분에서도 사용 할 수 있다, 어셈블러가 코드를 두 번 읽는 (2패스 어셈블러)방식으로 해결, 1차로 기호 테이블 만들고 2차로 코드를 읽을때 2진 코드를 생성
  1) 1패스 -> 줄 번호를 추적하며 어셈블리를 훑는다. 번호는 0시작, A-명령어나 C-명령어를 만나면 번호 +=1, 주석이나 레이블 선언에는 유지하며 레이블 선언은 기호 테이블에 현재 줄 번호 더하기 1값을 연결시킨다(ROM주소가 됨)
  2) 2패스 -> 전체 프로그램을 다시 훑으며 각 라인을 파싱, 기호가 있는 A-명령어를 만나면 기호 테이블 조회하여 명령어 번역 완료 (기호 테이블에 없으면 새로운 변수)

#### 구현
핵 어셈블러는 다음과 같이 명령줄 인수를 받는다
`prompt > HackAssembler Prog.asm` Prog.asm에 어셈블리 명령어가 담겨있음 -> Prog.hack이라는 2진 명령어 출력파일을 만들어냄.

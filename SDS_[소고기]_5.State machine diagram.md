# 5. State machine diagram

 이 장은 시스템의 State machine diagram을 그리고 설명한다. <br> 아래 [그림 5-1]은 client의 State machine diagram이고, [그림 5-2]는 server system의 State machine diagram이다.

<div align="center">
  <img width="779" height="570" alt="Image" src="https://github.com/user-attachments/assets/6b311528-df7e-4744-ba2d-0ec24e3e8a48" /> <br>
  [그림 5-1] Client의 State machine diagram
</div> <br>

### 클라이언트 상태 머신 다이어그램 설명
본 앱의 State는 크게 LoggedOut(로그아웃됨), Authenticating(인증 중), Main(메인), Restriction(제한 실행), Withdrawing(회원 탈퇴 중)으로 구분되며, 사용자가 앱을 처음 실행하면 LoggedOut 상태에서 시작한다.

LoggedOut 상태는 사용자가 로그인되어 있지 않은 상태로, 로그인 화면이나 회원가입 화면을 띄운다. <br>
이 상태에서 사용자가 '회원가입'을 선택하고 정보를 입력하면 Registering(회원가입 중) 상태로 전환된다. 서버로부터 아이디 중복 등의 오류를 받으면 다시 LoggedOut 상태로 돌아가 오류 메시지를 표시하고, 회원가입에 성공하면 Main 상태로 전환된다.

Authenticating(인증 중) 상태는 사용자가 아이디와 비밀번호를 입력하고 '로그인'을 시도하는 상태이다. 인증에 성공하면 사용자의 정보를 불러와 Main 상태로 전환되고, 등록되지 않은 아이디이거나 비밀번호가 틀리면 오류 메시지를 표시하며 LoggedOut 상태로 복귀한다.

Main 상태는 로그인한 사용자가 앱의 핵심 기능을 사용하는 복합 상태이다. 이 상태에 진입하면 기본적으로 남은 시간/영상 개수 등을 보여주는 Dashboard(대시보드) 하위 상태가 된다. <br>
Main 상태에서는 사용자의 행동에 따라 여러 하위 상태로 이동할 수 있다. 커뮤니티 탭을 누르면 Community(커뮤니티) 상태가 되어 글을 작성하거나 읽을 수 있으며, 프로필 탭을 누르면 Profile(프로필) 상태가 되어 업적이나 닉네임을 관리할 수 있다. 또한, 제한할 앱 선택이나 제한 조건을 설정하기 위해 ManagingSettings(설정 관리) 상태로 이동할 수 있다. <br>
Main 상태에서 사용자가 설정 탭의 '로그아웃' 버튼을 누르면 앱은 로그인 화면을 표시하는 LoggedOut 상태로 돌아간다. <br>
Main 상태에 있는 동안, 백그라운드에서는 사용자의 숏폼 시청을 감지한다. 이 서비스가 사용자의 시청 시간이나 개수가 설정된 한도를 초과했음을 감지하면, 앱은 Restriction(제한 실행) 상태로 강제 전환된다.

Restriction 상태는 사용자에게 제한이 발동되었음을 알리는 상태이다. 이 상태에서는 설정된 제한 강도에 따라 동기부여 문구를 띄우거나 앱을 종료시키는 등의 동작이 수행된다. 사용자가 이 경고를 확인하면 앱은 다시 Main 상태로 돌아가 모니터링을 지속한다.

사용자가 '회원탈퇴' 버튼을 누르면 Withdrawing(회원 탈퇴 중) 상태가 된다. 회원 탈퇴가 최종 완료되면 LoggedOut 상태로 전환되고, 서버 오류 등의 문제로 회원 탈퇴에 실패하면 Main 상태로 돌아간다.

사용자가 어떤 상태에서든 앱 종료를 시도하면 앱은 완전히 종료된다.

<div align="center">
  <img width="729" height="551" alt="Image" src="https://github.com/user-attachments/assets/9d39d5f5-8dc2-40da-83db-84ff7cc9e5e7" /> <br>
  [그림 5-2] Server system의 State machine diagram
</div> <br>

### 서버 상태 머신 다이어그램 설명
서버의 State는 데이터베이스에 저장된 개별 사용자 계정의 생명주기를 나타낸다. 

모든 계정은 NonExistent(계정 없음) 상태에서 시작한다. <br> NonExistent 상태는 특정 사용자 정보가 데이터베이스에 존재하지 않는 상태이다. 이 상태에서 클라이언트로부터 회원가입 요청을 받으면 서버는 CreatingAccount(계정 생성 중) 상태로 전환된다.

CreatingAccount(계정 생성 중) 상태에서는 요청된 아이디의 중복 여부를 검사한다. 만약 아이디가 중복되면 오류를 반환하고 다시 NonExistent 상태가 되고, 유효한 요청일 경우 사용자 정보를 데이터베이스에 저장하고 Active_LoggedOut 상태로 전환된다.

Active_LoggedOut 상태는 계정은 존재하지만, 현재 로그인된 세션이 없는 상태이다. <br>
이 상태에서 클라이언트로부터 로그인 요청을 받으면 서버는 Authenticating(인증 중) 상태가 된다.

Authenticating(인증 중) 상태에서는 요청된 아이디와 비밀번호가 DB의 정보와 일치하는지 검사한다. 정보가 일치하지 않으면 오류를 반환하고 Active_LoggedOut 상태로 돌아가고, 정보가 일치하면 Active_LoggedIn 상태로 전환된다.

Active_LoggedIn 상태는 사용자가 유효한 세션을 가지고 서버와 통신할 수 있는 주된 상태이다. <br>
서버는 이 상태에서 커뮤니티 글 작성, 제한 설정 변경, 목표 달성 확인, 프로필 수정 등 클라이언트의 다양한 API 요청을 처리(Processing Request)할 수 있다. 요청 처리가 완료되면 다시 Active_LoggedIn 상태로 돌아와 다음 요청을 기다린다. <br>
만약, 이 상태에서 클라이언트로부터 '로그아웃' 요청을 받거나 세션 토큰이 만료되면 서버는 세션을 무효화하고 Active_LoggedOut 상태로 전환된다.

만약 클라이언트로부터 '회원 탈퇴' 요청을 받으면 서버는 DeletingAccount(계정 삭제 중) 상태로 전환된다. <br> '회원 탈퇴' 과정에 성공하면 해당 사용자와 관련된 모든 정보(프로필, 글, 댓글, 리워드 내역 등)를 데이터베이스에서 영구히 삭제하고, 해당 계정은 다시 NonExistent 상태가 된다. 만약 '회원 탈퇴' 과정에 실패하면, Active_LoggedIn 상태로 전환된다.

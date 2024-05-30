# Dreamhack CTF Season 5 Round #10

이번에 풀 Dreamhack wargame 문제는 web 분야의 pharmacy 문제이다. 처음 writeup과는 달리 웹을 어느 정도 공부했기 때문에 문제를 다 풀지는 못하더라도 코드를 분석하고 공부할 수는 있을 것이라고 생각한다.

문제를 다운받아보니 다음과 같은 파일들이 있는 것을 확인했다.
![image](https://github.com/hyozii/Writeup_May-/assets/163365936/a679b99a-facd-49d4-86d4-93593dba76a9)
![image](https://github.com/hyozii/Writeup_May-/assets/163365936/4975e614-c6f0-4e2d-856f-d9c606942922)
![image](https://github.com/hyozii/Writeup_May-/assets/163365936/8197f61b-db16-4984-bdbd-341d894da9be)
![image](https://github.com/hyozii/Writeup_May-/assets/163365936/5172a40e-abc4-4211-8d9a-b53e8c52b3e1)

 먼저 Dockerfile을 먼저 열어보자.Dockerfile은 docker에서 이미지를 생성하기 위한 용도로 작성하는 파일이다. 
 FOME은 생성할 이미지의 베이스가 될 이미지를 의미한다. 이 Dockerfile은 PHP 7.4 버전과 Apache 웹 서버가 포함된 공식 Docker 이미지를 베이스로 사용한다. 
COPY는 스크립트를 디렉토리에 복사하고, RUN은 새로운 레이어에서 명령어를 실행해 새로운 이미지를 생성한다. 따라서 COPY ./deploy/run.sh /usr/sbin/
RUN chmod +x /usr/sbin/run.sh는 run.sh 스크립트를 컨테이너의 /usr/sbin/디렉토리에 복사하고, 실행 권한을 부여한다. 


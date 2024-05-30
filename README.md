# Dreamhack CTF Season 5 Round #10

이번에 풀 Dreamhack wargame 문제는 web 분야의 pharmacy 문제이다. 처음 writeup과는 달리 웹을 어느 정도 공부했기 때문에 문제를 다 풀지는 못하더라도 코드를 분석하고 공부할 수는 있을 것이라고 생각한다.

문제를 다운받아보니 다음과 같은 파일들이 있는 것을 확인했다.

![image](https://github.com/hyozii/Writeup_May-/assets/163365936/a679b99a-facd-49d4-86d4-93593dba76a9)
![image](https://github.com/hyozii/Writeup_May-/assets/163365936/4975e614-c6f0-4e2d-856f-d9c606942922)
![image](https://github.com/hyozii/Writeup_May-/assets/163365936/8197f61b-db16-4984-bdbd-341d894da9be)
![image](https://github.com/hyozii/Writeup_May-/assets/163365936/5172a40e-abc4-4211-8d9a-b53e8c52b3e1)

1. Dockerfile 파일

![image](https://github.com/hyozii/Writeup_May-/assets/163365936/dc95769c-567e-4d24-a89e-37b5818e3f53)


먼저 Dockerfile을 먼저 열어보자.Dockerfile은 docker에서 이미지를 생성하기 위한 용도로 작성하는 파일이다. 
FOME은 생성할 이미지의 베이스가 될 이미지를 의미한다. 이 Dockerfile은 PHP 7.4 버전과 Apache 웹 서버가 포함된 공식 Docker 이미지를 베이스로 사용한다.

COPY는 <복사할 파일 경로> <이미지에서 파일이 위치할 절대 경로> 형식으로 이루어지고, 디렉토리로 복사한다. RUN은 새로운 레이어에서 명령어를 실행해 새로운 이미지를 생성한다. 따라서 COPY ./deploy/run.sh /usr/sbin/는 run.sh 스크립트를 컨테이너의 /usr/sbin/ 디렉토리에 복사한다는 뜻이고, RUN chmod +x /usr/sbin/run.sh run.sh는 이에 실행 권한을 부여한다는 뜻이다.
 
 
다음으로 COPY ./deploy/src /var/www/html와 RUN chmod 777 /var/www/html/uploads도 비슷하게 해석할 수 있는데, ./deploy/src 디렉토리의 소스 코드를 컨테이너의 /var/www/html 디렉토리에 복사하고 /var/www/html/uploads 디텍토리에 777권한을 부여한다는 뜻이다. 이때 777은 리눅스 스터디 때 배웠던 것처럼 owner/group/others에 Read, Write, Access 권한을 모두 부여한다는 뜻이다.

COPY ./deploy/flag.txt /는 ./deploy/flag.txt /파일을 컨테이너의 루트 디렉토리에 복사한다는 뜻이다.

마지막으로 EXPOSE 80와 CMD ["/usr/sbin/run.sh"] 에 대해 살펴보자면, 컨테이너의 80번 포트를 노출하고, 컨테이너가 시작될 때 []의 스크립트를 실행하도록 하라는 뜻이다.

결국 이 Dockerfile은 이미지를 생성하기 위한 파일로 어떤 웹 서버를 포함하는지, 어떤 소스 코드를 어떤 컨테이너에 복사하는지, 어떤 포트를 노출하는지, 실행 시에 어떤 스크립트를 실행하는지를 나타내는 파일이다. 내가 이 파일을 잘 모르기 때문인지, 아니면 정말 문제 풀이에는 별로 큰 영향을 미치지 않기 때문인지는 몰라도 지금 나에게는 도움이 되는 정보가 없는 것 같았다.

2. flag.txt

![image](https://github.com/hyozii/Writeup_May-/assets/163365936/136ebb43-a35b-4b25-a033-2e39d670eadb)

다음으로 flag.txt를 열어보니 placeholder라는 단어가 들어있다. placeholder는 웹을 공부할 때 입력 필드에 사용자가 올바른 값을 넣을 수 있도록 힌트를 줄 때 사용하는 것'이었다. 이번 문제 파일 안에 이름이 'placeholder'인 파일이 있는데, 그럼 그 파일이 힌트라는 뜻일까? flag를 얻기 위해서는 저 파일을 이용해야 한다는 뜻일까? 일단 placeholder 파일이 중요하다는 사실을 확인하고 넘어가보자.

3. run.sh

![image](https://github.com/hyozii/Writeup_May-/assets/163365936/238c30d3-f060-4e9f-a788-c8177446160e)

.sh 파일은 주로 셸 스크립트 파일의 확장자로 사용되고, 셸 스크립트 파일은 기본 명령어나 간단한 문법을 사용해 사용자가 작성하는 스크립트 파일이다. 각 줄의 코드를 해석해보자면,

#!/bin/bash는 이 스크립트가 bash 셸에서 실행되어야 한다는 것을 알려준다. 그리고 export FLAG="test"는 export 명령어를 사용해 환경 변수를 설정한 것이다. 따라서 해당 코드는 FLAG 환경 변수가 test로 설정된 것이다. 다른 워게임 문제들도 이런 식으로 플래그를 찾는 방식이라고 하는데, flag 환경 변수를 찾아내고, 그 값이 실제로 플래그인지 확인해야 한다고 한다.

마지막으로, &>/dev/null /usr/sbin/apachectl -DFOREGROUND -k start는 Apache 웹 서버를 실행해준다. 웹 서버를 실행할 때 필요한 코드인 것 같다.

이 파일을 통해 플래그를 얻기 위해서는 어디를 확인해봐야 하는지를 알 수 있었다.

4. index.php

php 파일이니 저번에 배운대로 확장자를 html로 바꿔보자. 그러면 파일의 아이콘이 크롬 아이콘으로 바뀐다. 이 파일을 클릭하면 다음과 같은 웹 브라우저가 나온다.

![image](https://github.com/hyozii/Writeup_May-/assets/163365936/3b1f1aab-4145-4ccc-8701-89de0d62e750)

약국 테마의 웹 브라우저인만큼 '약국, 당신의 처방전을 gif 형식으로 업로드하세요'라고 쓰여있다. 플래그를 얻기 위해서는 문제 파일 중 하나를 gif 형식으로 바꿔서 저 파일에 넣어야 하는 것 같다.

![image](https://github.com/hyozii/Writeup_May-/assets/163365936/7fc8e736-8a7a-4a44-b8e7-1ee4acf9cded)

혹시 몰라 placeholder 파일을 .gif 형식으로 바꾸고 업로드해 보았다. 

![image](https://github.com/hyozii/Writeup_May-/assets/163365936/6ab2488a-e002-43dd-87cf-d2af1e50a811)

.gif 형식으로 바꾸지 않아 업로드에 실패했다는 메시지가 뜬다. 이렇게 바꾸는 건 안될 것 같고, html에서 코드를 추가해 php 파일을 gif 형식으로 변형해줘야 하는 것 같다. 일단 내가 생각하기에 이 문제를 푸는 방법은 문제 파일 중 하나를 gif 파일로 변환해 웹 브라우저에 업로드하는 것인데, 이 방법이 맞는지도 모르겠다....

일단 마저 코드를 분석해보자. 



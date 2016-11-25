---
layout: post
title:  "리눅스 파헤치기 - #파일/폴더 권한"
description: "리눅스 완전정복 프로젝트 #1"
author: codebasik
date:   2016-11-24 09:00:00 +0900
categories: linux
published: true
---

>리눅스 환경에 익숙하지 않아 아주 기본적인 것 부터 시작해보려고 합니다.<br/>
 파일권한은 어떻게 구성되어 있는지? 어떻게 변경하는지 알아봅니다. 

### 파일/폴더 권한

![예시]({{ site.url }}/img/2016-11-24/linux_chmod_01.png){: style="width:60%; display: block; auto 0; "}

```
 —          rwx         rwx           rwx
 파일타입    user 권한    group 권한    other 권한
```
파일에는 3개의 권한이 표시되며<br/>
각각 유저(user)  / 그룹(group) / 모든 사용자 (other)를 나타냅니다.<br/>
 * user : 파일의 소유자
 * group : 파일이 포함된 그룹
 * other : 그 외 나머지 사용자

rwx 문자열은 각각, 읽기(Read), 쓰기(Write), 실행(Execute)을 의미하며,<br/>
권한이 있는 경우 r / w / x 로 표시하고, 권한이 없을 경우엔 - 로 표시합니다.

예시)
 * rwx : 파일 소유주는 읽고, 쓰고, 실행 가능하다.
 * r-x : 파일 그룹의 유저는, 읽기와 실행만 가능하다.
 * r-- : 나머지 유저는, 읽기만 가능하다.	

파일/폴더의 권한을 변경하기 위해서는 chmod 명령어를 아래와 같이 사용합니다. (chmod = “change mode”)<br/>
chmod [권한] [파일]
 * chmod g+w test      # test 파일에, 그룹(g) 쓰기(w) 권한 추가(+) 
 * chmod o-x test      # test 파일에, 나머지 사용자(o)의 실행(x) 권한 제거(-)

여러개의 심볼을 묶어서 한번에 변경할 수도 있습니다. 
 * chmod u+rwx test    # user 에 rwx 권한 추가.
 * chmod ugo+rx test    
 * chmod u+x,g+rw,o-r test

하지만, 현업에서는 위와 같이 권한을 부여하는 경우는 거의 없습니다.<br/>
심볼 대신에 숫자를 사용합니다.
 * r = 4
 * w = 2
 * x = 1 
 * — = 0 

조금 더 간편하게는 각 그룹에 대한 권한을 숫자를 합한 값으로 한자리로 표현할 수 있습니다.<br/>
 *  rw- = 4 + 2 + 0 = 6
 *  r-x = 4  + 0 + 1 = 5
 *  rwx = 4 + 2 + 1 = 7

```
chmod 755 test     # test파일의 권한을 rwxr-xr-x 로 설정한다.
chmod 654 test     # 654 = rw-r-xr--
chmod 4 test       # chmod 004 test
```



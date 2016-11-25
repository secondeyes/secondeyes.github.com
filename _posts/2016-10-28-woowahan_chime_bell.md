---
layout: post
title:  "AWS IoT 버튼을 활용하여 차임벨 만들기"
description: "AWS 서비스, LINE Messaging API, 그리고 약간의(?) 인력을 활용하여 불편함을 해결하기"
author: ikkang
date:   2016-10-28 09:00:00 +0900
categories: study
published: true
ogimage: /img/2016-10-28/IMG_1587.JPG
---

![두드리지 마세요]({{ site.url }}/img/2016-10-28/IMG_1586.JPG){: style="width:50%; display:block; border:1px solid #021a40; margin:40px auto 0;"}
<br/>
우아한형제들 사무실 입구에는 이런 안내문이 붙어 있습니다.<br/>
사원증을 깜박하고 출근한 직원들이 문을 두드리면 문 근처의 직원들이 일에 집중하기 힘들다는 것이 그 이유였는데요.
재치있고 재밌게 쓰여 있지만 결국 한줄로 요약하면 ‘문 두드리지 마세요.’인 것.<br/>
하지 말라고 하는 것보다 더 좋은 방법이 없을까를 고민하던 중 일전에 재있을 것 같아 구입해서 테스트만 잠깐 해보고 서랍에 고이 모셔둔 AWS IoT 버튼이 생각났습니다.<br/>
![질러라]({{ site.url }}/img/2016-10-28/jirm.jpg){: style="width:50%; display:block; border:1px solid #021a40; margin:40px auto 0;"}
<p align="center">지름교 신자들은 일단 지른 다음 용도는 나중에 생각합니다.</p>
<br/>
마침 타이밍 좋게 Line Bot API에 단체방에 메세지를 보낼 수 있는 API도 추가되었습니다. 게다가 먼저 삽질을 다 해본 팀원이 있어 삽질해야 할 위험도 줄었습니다. 
~~간절히 바라니 우주가 도와주는 것만 같습니다.~~
<br/>
원하는 기능은 아래와 같습니다.<br/>

 * 출입카드가 없는 사람이 AWS IoT 버튼을 누르면
 * AWS Lambda 서비스에서 Line Bot API를 이용하여 라인 팀 대화방에 알림메세지를 보내고
 * 이 메세지를 확인한 사람이 문을 열어줍니다.
 

이를 위해서는 AWS IoT, Lambda와 라인의 Line Bot API를 사용하면 될 것 같습니다.(나중에 추가로 AWS API Gateway도 사용해야 했습니다.)

![AWS IoT Button 추가]({{ site.url }}/img/2016-10-28/awsIoT.png){: style="width:70%; display:block; border:1px solid #021a40; margin:40px auto 0;"}

AWS IoT 버튼은 AWS IoT 서비스를 사용합니다. 위사진의 빨간 원으로 표시된 Connect AWS IoT Button을 클릭한 다음<br/>
이어지는 설명에 따라서 차분히 진행하면 모든 세팅이 완료됩니다.

![자세한 설명은 생략한다.]({{ site.url }}/img/2016-10-28/daetul_no_more_explain.jpg){: style="width:35%; display:block; border:1px solid #021a40; margin:40px auto 0;"}

서비스를 연동하기 위해 Lambda를 연결하여 프로그래밍 하도록 합시다. 
Line Bot API를 사용하기 위해서는 일단 [Line Business Center](https://business.line.me/ko/)에 계정을 만듭니다. 
현재 LINE@, Line Login, Messaging API 세가지 서비스를 제공하고 있는데 라인봇을 만들기 위해서는 Messaging API를 사용하면 됩니다.<br/>

![계정등록]({{ site.url }}/img/2016-10-28/linebot_1.png){: style="width:70%; display:block; border:1px solid #021a40; margin:40px auto 0;"}

계정의 이름과 사진 업종 정보를 입력하면 계정이 생성됩니다. 
생성한 다음 추가로 해야 할 작업은 WebHook URL을 구해서 입력하는 것과 Channel Access Token을 얻는 것 두가지만 하면 됩니다. 
Channel Access Token은 Rest API로 메세지를 보낼 때 인증을 위해 필요합니다. WebHook URL은 Messaging API만 사용할 때는 따로 사용할 필요는 없습니다만...

![샘플코드]({{ site.url }}/img/2016-10-28/linebot_3.png){: style="width:50%; display:block; border:1px solid #021a40; margin:40px auto 0;"}

메세지를 보낼 때 어디로 보낼지를 알리기 위해 JSON Data에 ‘to’에 키값을 알아야 하는데 이 키 값은  다른곳에서는 알 수 없습니다. 
WebHook URL을 세팅해두면 Bot이 Line 서버에서 받는 데이터들이 해당 URL로 JSON 포맷으로 전달됩니다. 
별도로 사용하는 서버가 있으면 서버에 간단한 프로그램으로 Body Data를 확인할 수 있는 URL을 확보해서 확인해도 되지만 
전 노는 서버도 없고 Web 프로그램을 해본지 오래되어 기억이 나지 않기때문에 간단히 AWS API Gateway를 사용하여 Lambda에서 간단히 확인해보기로 했습니다. 
(우아한 형제들은 개발자들이 자유롭게 테스트하기 위해 놀이터라고 부르는 AWS 서비스를 무제한으로 사용할 수 있는 계정을 제공하고 있습니다.)

![Lambda Sample]({{ site.url }}/img/2016-10-28/linebot_4.png)<br/>
이 코드에서 남기는 Console Log는 Cloud Watch에서 확인할 수 있습니다.
![Cloud Watch]({{ site.url }}/img/2016-10-28/linebot_5.png){: style="width:60%; display:block; border:1px solid #021a40;"}

groupId(일반 대화방은 roomId)를 구했으니 Lambda를 이용하여 LINE Messaging API를 호출하는 코드를 작성해서 연결해주면 됩니다. 
위의 CURL 샘플에 있다시피 헤더에 인증키를 설정하고 Body에 정해진 JSON 포맷으로 전달하기만 하면 되는 심플한 구성입니다. 그래서…

![자세한 설명은 생략한다.]({{ site.url }}/img/2016-10-28/daetul_no_more_explain.jpg){: style="width:35%; display:block; border:1px solid #021a40; margin:40px auto 0;"}

는 훼이크고 본 글 말미에 Node.js로 작성된 소스를 다운받을 수 있는 링크가 있습니다.
(거듭 말씀드리지만 전 프론트, 서버를 망라하여 Web을 해본지 정말 오래된 개발자입니다. 참고용으로만 사용하세요.)


이렇게 하면 대략 개발해야할 항목은 끝났습니다. 작성한 Bot을 친구로 추가한 다음 사용할 단체방에 초대해줍니다.
![Invite]({{ site.url }}/img/2016-10-28/linebot_6.png){: style="width:40%; display:block; border:1px solid #021a40; margin:40px auto 0;"}
![버튼을 붙인다]({{ site.url }}/img/2016-10-28/IMG_1587.JPG){: style="width:40%; display:block; border:1px solid #021a40; margin:0px auto 0;"}
버튼을 문앞에 붙이고 안내문을 붙인다음 전체방에 공지를 하면…

![Beta Service]({{ site.url }}/img/2016-10-28/linebot_7.png){: style="width:40%; display:block; border:1px solid #021a40; margin:40px auto 0;"}

첫날은 이렇게 장난벨이 폭주합니다.(아휴 이런 촌것들…)<br/>

이 글을 쓰는 지금 이틀이 지나고 안정적인 상황으로 접어들었습니다. 뭔가 싶어 눌러보는 사람도 이제 별로 없고 조금 더 살펴본 후 안정적으로 서비스가 제공된다고 판단되면 적당한 단체방으로 이관하여 사용할 예정입니다.
LINE API나 AWS IoT와 같이 심플하고 쉽게 접근할 수 있는 서비스를 엮어 거기에 약간의 아이디어를 더해 재미있고 편리한 서비스를 만들어 내는 것이 최근 뜨거운 IoT서비스의 묘미가 아닌가 합니다.

모두가 만족하는 해법은 반드시 존재합니다.<br/>
감사합니다.<br/>

[Source Code Download]({{ site.url }}/files/2016-10-28/OpenTheDoor.js)
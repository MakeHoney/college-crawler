# 크롤링 삽질 리포트

루비 기반으로 학교 여러 홈페이지 각각에 대한 크롤링 모듈을 하나씩 만들 생각이다.
언젠가는 내 앱에 사용할 날이 오겠.. 올까.

학식 페이지
---------

아주대학교 학식 페이지 html 문서는 정말 정말 정말 정말 조잡스럽게 되어있었다...
'그래도 Nokogiri로 크롤링하면 알아서 척척 파싱해 주겠지'라고 생각했었으나,
그러지 못했다. 사실 공백이나 개행문자는 크롤링 하는데 문제가 되지 않았을 것이다.
아마 추측이지만 아래 드래그된 부분(조식/백반)의 꺽쇠가 문제가 되지 않았나 싶다.

<img src="https://user-images.githubusercontent.com/31656287/38167157-a7719418-356b-11e8-95bf-c5a8160a9b9d.png">

실제 코드를 보면 아래와 같이 되어있다. (@#!@$!@$@!%!#%!)

<img src="https://user-images.githubusercontent.com/31656287/38167158-a8b32e72-356b-11e8-950a-1a8d966f4a8e.png">

구글링을 해보니 html에서는 특수기호(꺽쇠 등)을 표현하기 위해서는 엔터티코드로 
표현을 해야한다고 한다. 하지만 개발자가 이러한 점을 고려하지 않은 것 같다. 4시간 동안 삽질하게 해줘서 고마워요 ㅎ...
(그렇다고 매일 식단을 업데이트하는 교직원 누나가 엔터티코드를 입력할 수가, 그럴리가 없잖아...)
아무튼 각설하고, 위 상황을 예로 들면 아래와 같이 코드가 작성되도록 백..단에서 신경써줘야한다.

<img src="https://user-images.githubusercontent.com/31656287/38167159-aa4524b6-356b-11e8-97d9-3daf2168ef1e.png">

그렇지 않으면 나같은 사람이 크롤링을 할 경우... 아래와 같이 식단의 맨 앞글자가 짤리는 수가 있다...(주된 문제점은 이게 아니었지만)
추된장국..추김치..추장불고기... 추장이 먹는 불고긴가... ㅈㅅ

<img src="https://user-images.githubusercontent.com/31656287/38167187-323177b2-356c-11e8-92f2-b9366467fc1b.png">

아무튼 해결 법은 간단했다. 아니 사실 네시간 동안 헤딩하면서 알아냈다. Nokogiri에는
여느 Prettifier와 비슷한 역할을 하는 메소드가 존재했다! .to_xhtml()이라는 메소드를 통해
인덴테이션을 정리한 뒤에 아래와 같이 새로운 변수에 저장을 하면 문자열 형태로 반환을 받는다.
그리고 이 변수에 저장된 값을 콘솔에 뿌린 뒤 문제시 되는 부분을 검사하던 중 코드에
13번에 해당하는 엔터티 코드가 삽입되어 있는 것을 발견하였다. 

<img src="https://user-images.githubusercontent.com/31656287/38167188-34355844-356c-11e8-91e6-0adc034c33cb.png">

본능적으로 저부분을 지우면 코드가 돌지 않을까 싶었고 gsub메소드를 통해서 문자열로부터 해당 엔터티코드를 싹다 지워버렸다.
Nokogiri::HTML(open(url))가 반환하는 타입은 문자열이 아니라 Nokogiri::HTML::Document
이기 때문에 새 변수에 해당 문자열을 인자로 Nokogiri::HTML()을 대입하였다.
그리고! 이 변수를 통해서 크롤링을 하니 문제 없이 크롤링이 되었다. 기뻤다. 끝.

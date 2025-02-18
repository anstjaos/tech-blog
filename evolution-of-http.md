# HTTP의 진화

HTTP(HyperText Transfer Protocol)는 월드 와이드 웹의 기본 프로토콜입니다. 
팀 버너스-리와 그의 팀이 1989-1991년 사이에 개발한 HTTP는 유연성을 형성하는 동시에 단순성을 유지하는 데 도움이 되는 많은 변화를 거쳤습니다.
아래 내용들을 통해 HTTP가 반신뢰 실험실 환경에서 파일을 교환하도록 설계된 프로토콜에서 고해상도 및 3D로 이미지와 비디오를 전달하는 현대식 인터넷 미로로 어떻게 진화했는지 알아보세요.

# 월드 와이드 웹의 발명
1989년, CERN에서 일하는 동안 Tim Berners-Lee는 인터넷에 하이퍼텍스트 시스템을 구축하는 제안을 작성했습니다. 
처음에는 Mesh라고 불렸지만, 1990년에 구현하면서 나중에 _World Wide Web_ 으로 이름이 바뀌었습니다. 
기존 TCP 및 IP 프로토콜을 기반으로 구축되었으며, 4개의 빌딩 블록으로 구성되었습니다.

* 하이퍼텍스트 문서를 표현하는 텍스트 형식인 **하이퍼텍스트 마크업 언어(HTML)**입니다.
* 이러한 문서를 교환하기 위한 간단한 프로토콜은 **하이퍼텍스트 전송 프로토콜(HTTP)**입니다.
* 이러한 문서를 표시하고 편집하는 클라이언트는 _WorldWideWeb_ 이라고 불리는 최초의 웹 브라우저입니다.
* 문서에 대한 접근 권한을 부여하는 서버, httpd 의 초기 버전입니다.

이 네 가지 구성 요소는 1990년 말에 완성되었고, 첫 번째 서버는 1991년 초에 CERN 외부에서 실행되었습니다. 
1991년 8월 6일, 팀 버너스-리는 공개 alt.hypertext 뉴스그룹에 게시했습니다. 
이는 현재 공개 프로젝트로서 월드 와이드 웹의 공식 시작으로 간주됩니다.

그 초기 단계에 사용된 HTTP 프로토콜은 매우 간단했습니다. 
나중에 HTTP/0.9로 명명되었고 때로는 원라인 프로토콜이라고도 불립니다.

# HTTP/0.9 – The one-line protocol
HTTP의 초기 버전에는 버전 번호가 없었습니다. 
나중에는 이후 버전과 구별하기 위해 0.9라고 불렸습니다. 
HTTP/0.9는 매우 간단했습니다. 
요청은 단일 행으로 구성되었고 GET 리소스 경로가 뒤따르는 유일한 가능한 메서드로 시작했습니다. 
서버에 연결되면 프로토콜, 서버 및 포트가 필요하지 않기 때문에 전체 URL은 포함되지 않았습니다.

```http request
GET /my-page.html
```

응답 역시 매우 간단했습니다. 파일 자체만 있었을 뿐이었습니다.

```html
<html>
  A very simple HTML page
</html>
```

이후의 진화와 달리 HTTP 헤더가 없었습니다. 즉, HTML 파일만 전송할 수 있었습니다. 
상태나 오류 코드는 없었습니다. 문제가 있으면 특정 HTML 파일이 생성되어 인간이 사용할 수 있도록 문제에 대한 설명이 포함되었습니다.

# HTTP/1.0 – Building extensibility
HTTP/0.9는 매우 제한적이었지만 브라우저와 서버는 이를 빠르게 더욱 다재다능하게 만들었습니다.

* 버전 정보는 각 요청 내에 전송되었습니다. (`HTTP/1.0`이 `GET` 줄에 추가되었습니다.)
* 응답의 시작 부분에 상태 코드 줄도 전송되었습니다. 이를 통해 브라우저 자체가 요청의 성공 또는 실패를 인식하고 그에 따라 동작을 조정할 수 있었습니다. 예를 들어, 특정 방식으로 로컬 캐시를 업데이트하거나 사용하는 것입니다.
* HTTP 헤더의 개념은 요청과 응답 모두에 도입되었습니다. 메타데이터를 전송할 수 있었고 프로토콜은 매우 유연하고 확장 가능해졌습니다.
* `Content-Type` 헤더 덕분에 일반 HTML 파일 외의 문서도 전송할 수 있습니다.

이 시점에서 일반적인 요청과 응답은 다음과 같습니다.

```http request
GET /my-page.html HTTP/1.0
User-Agent: NCSA_Mosaic/2.0 (Windows 3.1)

HTTP/1.0 200 OK
Date: Tue, 15 Nov 1994 08:12:31 GMT
Server: CERN/3.0 libwww/2.17
Content-Type: text/html
<HTML>
A page with an image
  <IMG SRC="/my-image.gif">
</HTML>
```

그 다음에는 두 번째 연결이 이어졌고 이미지를 가져오라는 요청(해당 응답 포함)이 이어졌습니다.

```http request
GET /my-image.gif HTTP/1.0
User-Agent: NCSA_Mosaic/2.0 (Windows 3.1)

HTTP/1.0 200 OK
Date: Tue, 15 Nov 1994 08:12:32 GMT
Server: CERN/3.0 libwww/2.17
Content-Type: text/gif
(image content)
```

1991년에서 1995년 사이에 try-and-see 방식으로 도입되었습니다. 
서버와 브라우저가 기능을 추가하고 그것이 인기를 얻는지 확인했습니다. 
상호 운용성 문제가 흔했습니다. 
이러한 문제를 해결하기 위해 1996년 11월에 일반적인 관행을 설명하는 정보 문서가 게시되었습니다. 
이것은 RFC 1945 로 알려졌으며 HTTP/1.0을 정의했습니다.

# HTTP/1.1 – The standardized protocol
그 사이에 적절한 표준화가 진행 중이었습니다. 
이는 HTTP/1.0의 다양한 구현과 병행하여 발생했습니다. 
HTTP의 첫 번째 표준화된 버전인 HTTP/1.1은 HTTP/1.0 이후 불과 몇 달 후인 1997년 초에 공개되었습니다.

HTTP/1.1은 모호성을 해소하고 수많은 개선 사항을 도입했습니다.

* 연결을 재사용할 수 있어 시간이 절약되었습니다. 단일 원본 문서에 포함된 리소스를 표시하기 위해 단일 원본 문서를 여러 번 열 필요가 더 이상 없습니다.
* 파이프라이닝이 추가되었습니다. 이를 통해 첫 번째 요청에 대한 답변이 완전히 전송되기 전에 두 번째 요청을 보낼 수 있었습니다. 이를 통해 통신 지연 시간이 단축되었습니다.
* 청크별 응답도 지원되었습니다.
* 추가적인 캐시 제어 메커니즘이 도입되었습니다.
* 언어, 인코딩, 유형을 포함한 콘텐츠 협상이 도입되었습니다. 이제 클라이언트와 서버는 어떤 콘텐츠를 교환할지에 대해 합의할 수 있습니다.
* `Host` 헤더 덕분에 동일한 IP 주소에서 여러 도메인을 호스팅할 수 있는 기능이 가능해져 서버 배치가 가능해졌습니다.

하나의 연결을 통해 이루어지는 일반적인 요청 흐름은 다음과 같습니다.

```http request
GET /en-US/docs/Glossary/CORS-safelisted_request_header HTTP/1.1
Host: developer.mozilla.org
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.9; rv:50.0) Gecko/20100101 Firefox/50.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://developer.mozilla.org/en-US/docs/Glossary/CORS-safelisted_request_header

HTTP/1.1 200 OK
Connection: Keep-Alive
Content-Encoding: gzip
Content-Type: text/html; charset=utf-8
Date: Wed, 20 Jul 2016 10:55:30 GMT
Etag: "547fa7e369ef56031dd3bff2ace9fc0832eb251a"
Keep-Alive: timeout=5, max=1000
Last-Modified: Tue, 19 Jul 2016 00:59:33 GMT
Server: Apache
Transfer-Encoding: chunked
Vary: Cookie, Accept-Encoding

(content)

GET /static/img/header-background.png HTTP/1.1
Host: developer.mozilla.org
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.9; rv:50.0) Gecko/20100101 Firefox/50.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://developer.mozilla.org/en-US/docs/Glossary/CORS-safelisted_request_header

HTTP/1.1 200 OK
Age: 9578461
Cache-Control: public, max-age=315360000
Connection: keep-alive
Content-Length: 3077
Content-Type: image/png
Date: Thu, 31 Mar 2016 13:34:46 GMT
Last-Modified: Wed, 21 Oct 2015 18:27:50 GMT
Server: Apache

(image content of 3077 bytes)
```

HTTP/1.1은 1997년 1월에 RFC 2068 로 처음 공개되었습니다 .

# 20년 이상의 개발
HTTP의 확장성 덕분에 새로운 헤더와 메서드를 쉽게 만들 수 있었습니다.
HTTP/1.1 프로토콜은 1999년 6월에 공개된 RFC 2616과 HTTP/2가 출시되기 전인 2014년 6월에 공개된 RFC 7230 - RFC 7235의 두 번의 개정을 거쳐 개선되었지만 15년 이상 매우 안정적이었습니다.
HTTP/1.1은 2022년에 RFC 9110 으로 다시 업데이트되었습니다.
HTTP/1.1이 업데이트되었을 뿐만 아니라 모든 HTTP가 개정되어 이제 다음 문서로 분할되었습니다. 
모든 HTTP 버전에 적용되는 의미론(RFC 9110), 캐싱(RFC 9111), HTTP/1.1(RFC 9112), HTTP/2(RFC 9113), HTTP/3(RFC 9114). 
또한 이 사양은 마침내 인터넷 표준(STD 97)의 지위를 얻었는데, 이전에는 항상 제안/초안 표준이었습니다.

## 보안 전송을 위한 HTTP 사용
HTTP에 대한 가장 큰 변경은 1994년 말에 이루어졌습니다. 
컴퓨터 서비스 회사인 Netscape Communications는 기본 TCP/IP 스택을 통해 HTTP를 전송하는 대신 그 위에 추가 암호화된 전송 계층인 SSL을 만들었습니다. 
SSL 1.0은 대중에게 공개되지 않았지만 SSL 2.0과 그 후속인 SSL 3.0은 전자상거래 웹사이트를 만드는 것을 허용했습니다. 
이를 위해 서버와 클라이언트 간에 교환되는 메시지의 진위성을 암호화하고 보장했습니다. 
SSL은 결국 표준화되어 TLS가 되었습니다.

같은 기간 동안 암호화된 전송 계층이 필요하다는 것이 분명해졌습니다. 
웹은 더 이상 대부분 학술적 네트워크가 아니었고, 대신 광고주, 무작위 개인, 범죄자가 가능한 한 많은 개인 데이터를 놓고 경쟁하는 정글이 되었습니다. 
HTTP를 기반으로 구축된 애플리케이션이 더욱 강력해지고 주소록, 이메일, 사용자 위치와 같은 개인 정보에 대한 액세스가 필요해지면서 TLS는 전자 상거래 사용 사례 외부에서 필요하게 되었습니다.

## 복잡한 애플리케이션에 HTTP 사용
Tim Berners-Lee는 원래 HTTP를 읽기 전용 매체로 생각하지 않았습니다. 
그는 사람들이 원격으로 문서를 추가하고 이동할 수 있는 웹, 일종의 분산 파일 시스템을 만들고 싶었습니다. 
1996년경 HTTP는 작성을 허용하도록 확장되었고 WebDAV라는 표준이 만들어졌습니다. 
주소록 항목을 처리하는 CardDAV와 달력을 처리하는 CalDAV와 같은 특정 애플리케이션을 포함하도록 확장되었습니다. 
하지만 이러한 모든 *DAV 확장에는 결함이 있었습니다. 
서버에서 구현할 때만 사용할 수 있었습니다.

2000년에 HTTP를 사용하는 새로운 패턴이 고안되었습니다. 
표현적 상태 전송 (또는 REST)입니다. 
API는 새로운 HTTP 메서드를 기반으로 하지 않았지만 대신 기본 HTTP/1.1 메서드가 있는 특정 URI에 대한 액세스에 의존했습니다.
이를 통해 모든 웹 애플리케이션은 브라우저나 서버를 업데이트하지 않고도 API가 데이터를 검색하고 수정하도록 할 수 있었습니다. 
모든 필수 정보는 표준 HTTP/1.1을 통해 웹사이트가 제공하는 파일에 포함되었습니다. 
REST 모델의 단점은 각 웹사이트가 자체 비표준 RESTful API를 정의하고 이를 완전히 제어한다는 것입니다. 
이는 클라이언트와 서버가 상호 운용 가능한 *DAV 확장과 달랐습니다. RESTful API는 2010년대에 매우 일반화되었습니다.

2005년 이후로 웹 페이지에서 더 많은 API를 사용할 수 있게 되었습니다. 
이러한 API 중 일부는 특정 목적을 위해 HTTP 프로토콜에 대한 확장을 생성합니다.

* 서버가 브라우저에 가끔 메시지를 푸시하는 서버 전송 이벤트입니다 .
* WebSocket은 기존 HTTP 연결을 업그레이드하여 설정할 수 있는 새로운 프로토콜입니다.

## 웹의 보안 모델 완화
HTTP는 same-origin policy라고 알려진 웹 보안 모델과 독립적입니다. 
사실, 현재의 웹 보안 모델은 HTTP가 만들어진 후에 개발되었습니다! 
수년에 걸쳐 특정 제약 하에 이 정책의 일부 제한을 해제하는 것이 유용하다는 것이 입증되었습니다. 
서버는 새로운 HTTP 헤더 세트를 사용하여 이러한 제한을 얼마나, 언제 해제할 것인지 클라이언트에 전송했습니다.
이는 CORS(Cross-Origin Resource Sharing) 및 CSP(Content Security Policy)와 같은 사양에서 정의되었습니다.

이러한 대규모 확장 외에도 많은 다른 헤더가 추가되었으며, 때로는 실험적으로만 추가되었습니다. 
주목할 만한 헤더는 개인 정보 보호를 제어하는 Do Not Track(DNT) 헤더, X-Frame-Options, 그리고 Upgrade-Insecure-Requests와 그 외에도 많은 헤더가 있습니다.

# HTTP/2 – A protocol for greater performance
수년에 걸쳐 웹 페이지는 더욱 복잡해졌습니다. 
일부는 그 자체로 애플리케이션이 되었습니다. 
더 많은 시각적 미디어가 표시되었고 상호 작용을 추가하는 스크립트의 볼륨과 크기도 증가했습니다. 
훨씬 더 많은 데이터가 상당히 더 많은 HTTP 요청을 통해 전송되었고 이로 인해 HTTP/1.1 연결에 대한 복잡성과 오버헤드가 증가했습니다.
이를 설명하기 위해 Google은 2010년대 초에 실험적 프로토콜 SPDY를 구현했습니다. 
클라이언트와 서버 간에 데이터를 교환하는 이 대체 방식은 브라우저와 서버 모두에서 작업하는 개발자의 관심을 모았습니다. 
SPDY는 응답성을 높이고 중복 데이터 전송 문제를 해결하여 HTTP/2 프로토콜의 기반이 되었습니다.

HTTP/2 프로토콜은 몇 가지 면에서 HTTP/1.1과 다릅니다.

* 텍스트 프로토콜이 아니라 바이너리 프로토콜입니다. 수동으로 읽고 만들 수 없습니다. 이러한 장애물에도 불구하고 개선된 최적화 기술을 구현할 수 있습니다.
* 다중화된 프로토콜입니다. 동일한 연결을 통해 병렬 요청을 할 수 있어 HTTP/1.x 프로토콜의 제약을 제거합니다.
* 헤더를 압축합니다. 이는 종종 요청 집합에서 유사하기 때문에 전송되는 데이터의 중복과 오버헤드를 제거합니다. 

2015년 5월에 공식적으로 표준화된 HTTP/2 사용은 2022년 1월에 모든 웹사이트의 46.9%로 정점을 찍었습니다. 
트래픽이 많은 웹사이트는 데이터 전송 오버헤드와 그에 따른 예산을 절감하기 위해 가장 빠르게 도입되었습니다.

이 빠른 채택은 HTTP/2가 웹사이트와 애플리케이션에 대한 변경을 요구하지 않았기 때문일 가능성이 큽니다. 
이를 사용하려면 최신 브라우저와 통신하는 최신 서버만 있으면 되었습니다. 
채택을 트리거하는 데 제한된 그룹 집합만 필요했고, 레거시 브라우저와 서버 버전이 갱신됨에 따라 웹 개발자가 상당한 작업을 하지 않고도 자연스럽게 사용량이 증가했습니다.

# Post-HTTP/2 evolution
HTTP의 확장성은 여전히 새로운 기능을 추가하는 데 사용되고 있습니다. 특히, 2016년에 등장한 HTTP 프로토콜의 새로운 확장을 인용할 수 있습니다.

* Alt-Svc 지원은 주어진 리소스의 식별과 위치의 분리를 허용했습니다. 이는 더 똑똑한 CDN 캐싱 메커니즘을 의미했습니다.
* client hint가 도입되면서 브라우저나 클라이언트는 요구 사항과 하드웨어 제약에 대한 정보를 서버에 적극적으로 전달할 수 있게 되었습니다.
* `Cookie` 헤더에 보안 관련 접두사를 도입함으로써 보안 쿠키가 변경되지 않도록 보장하는 데 도움이 되었습니다.

# HTTP/3 - HTTP over QUIC
HTTP의 다음 주요 버전인 HTTP/3는 이전 버전의 HTTP와 동일한 의미론을 가지고 있지만 전송 계층 부분에 TCP 대신 QUIC를 사용합니다. 
2022년 10월까지 모든 웹사이트의 26%가 HTTP/3를 사용했습니다.

QUIC는 HTTP 연결에 대해 훨씬 더 낮은 지연 시간을 제공하도록 설계되었습니다. 
HTTP/2와 마찬가지로 다중화된 프로토콜이지만 HTTP/2는 단일 TCP 연결을 통해 실행되므로 TCP 계층에서 처리되는 패킷 손실 감지 및 재전송은 모든 스트림을 차단할 수 있습니다. 
QUIC는 UDP를 통해 여러 스트림을 실행 하고 각 스트림에 대해 독립적으로 패킷 손실 감지 및 재전송을 구현하므로 오류가 발생하면 해당 패킷에 데이터가 있는 스트림만 차단됩니다.

RFC 9114 에 정의된 HTTP/3는 Chromium(및 Chrome, Edge와 같은 변형)과 Firefox를 포함한 대부분의 주요 브라우저에서 지원됩니다.
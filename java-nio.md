# Java NIO에 대해

Java NIO(New Input/Output)는 Java 1.4에서 도입된 비동기 I/O API로, 기존의 전통적인 I/O 방식을 개선하여 더 효율적이고 확장 가능한 애플리케이션 개발을 가능하게 합니다.
이 글에서는 NIO의 핵심 개념부터 실제 활용 방법까지 자세히 알아보겠습니다.

## 기존 I/O vs NIO

### 기존 I/O의 한계
기존의 Java I/O는 블로킹(Blocking) 방식으로 동작합니다.

```java
// 기존 I/O - 블로킹 방식
InputStream input = socket.getInputStream();
int data = input.read(); // 데이터가 올 때까지 대기
```

이런 방식의 문제점은 다음과 같습니다.

1. 스레드가 I/O 작업 중에 블로킹되어 다른 작업을 수행할 수 없음
2. 많은 클라이언트를 처리하려면 스레드 수가 급격히 증가
3. 메모리 사용량과 컨텍스트 스위칭 비용 증가

### NIO의 장점
NIO는 논블로킹(Non-blocking) 방식으로 동작합니다.

1. 하나의 스레드로 여러 채널을 관리 가능
2. 메모리 효율성 향상
3. 높은 동시성 처리 능력

## NIO의 핵심 구성 요소

### 1. Channel (채널)
Channel은 데이터를 읽고 쓸 수 있는 연결통로입니다. 기존 I/O의 Stream과 달리 양방향 통신이 가능합니다.
```java
// FileChannel 예제
try (FileChannel fileChannel = FileChannel.open(Paths.get("example.txt"), StandardOpenOption.READ)) {
    ByteBuffer buffer = ByteBuffer.allocate(1024);
    int bytesRead = fileChannel.read(buffer);

    while (bytesRead != -1) {
        buffer.flip(); // 읽기 모드로 전환
    
        while (buffer.hasRemaining()) {
            System.out.print((char) buffer.get());
        }
        
        buffer.clear(); // 쓰기 모드로 전환
        bytesRead = fileChannel.read(buffer);
    }
}
```

주요 Channel 타입:

1. **FileChannel**: 파일 I/O
2. **SocketChannel**: TCP 클라이언트 연결
3. **ServerSocketChannel**: TCP 서버 연결 수락
4. **DatagramChannel**: UDP 통신

### 2. Buffer (버퍼)
Buffer는 데이터를 임시로 저장하는 컨테이너입니다. Channel과 함께 사용되어 데이터 전송을 담당합니다.
   
```java
// Buffer 기본 사용법
ByteBuffer buffer = ByteBuffer.allocate(1024);

// 데이터 쓰기
buffer.put("Hello NIO".getBytes());

// 읽기 모드로 전환
buffer.flip();

// 데이터 읽기
byte[] data = new byte[buffer.remaining()];
buffer.get(data);
String result = new String(data);

// 버퍼 초기화
buffer.clear();
```

Buffer의 주요 속성:

1. **Capacity**: 버퍼의 전체 크기
2. **Position**: 현재 읽기/쓰기 위치
3. **Limit**: 읽기/쓰기 가능한 마지막 위치
4. **Mark**: 특정 위치를 기억하는 마커

### 3. Selector (셀렉터)
Selector는 여러 채널을 하나의 스레드에서 모니터링할 수 있게 해주는 핵심 컴포넌트입니다.

```java
// Selector 기본 사용법
Selector selector = Selector.open();

// 채널을 셀렉터에 등록
ServerSocketChannel serverChannel = ServerSocketChannel.open();

serverChannel.configureBlocking(false);
serverChannel.bind(new InetSocketAddress(8080));
serverChannel.register(selector, SelectionKey.OP_ACCEPT);

while (true) {
    // 준비된 채널이 있는지 확인
    int readyChannels = selector.select();

    if (readyChannels > 0) {
        Set<SelectionKey> selectedKeys = selector.selectedKeys();
        Iterator<SelectionKey> keyIterator = selectedKeys.iterator();
        
        while (keyIterator.hasNext()) {
            SelectionKey key = keyIterator.next();
            
            if (key.isAcceptable()) {
                // 새로운 연결 수락
                handleAccept(key);
            } else if (key.isReadable()) {
                // 데이터 읽기
                handleRead(key);
            }
            
            keyIterator.remove();
        }
    }
}
```

## NIO.2 (Java 7)
Java 7에서는 NIO.2가 도입되어 더욱 편리한 파일 시스템 API를 제공합니다:

```java
// Path와 Files 클래스 사용
Path path = Paths.get("example.txt");

// 파일 읽기
List<String> lines = Files.readAllLines(path);

// 파일 쓰기
Files.write(path, "Hello NIO.2".getBytes());

// 디렉터리 탐색
try (Stream<Path> paths = Files.walk(Paths.get("."))) {
    paths.filter(Files::isRegularFile).forEach(System.out::println);
}

// 파일 변경 감시
WatchService watchService = FileSystems.getDefault().newWatchService();
path.getParent().register(watchService, StandardWatchEventKinds.ENTRY_MODIFY);
```

## 성능 비교 및 사용 시기
### NIO를 사용해야 하는 경우

1. 높은 동시성이 필요한 서버 애플리케이션 
   - 채팅 서버, 게임 서버, 웹 서버 등

2. 대용량 파일 처리 
   - 메모리 효율적인 파일 처리가 필요한 경우

3. 네트워크 애플리케이션 
   - 많은 클라이언트 연결을 처리해야 하는 경우

### 기존 I/O를 사용해야 하는 경우
1. 간단한 애플리케이션 
   - 복잡성이 불필요한 경우

2. 순차적 데이터 처리
   - 스트림 기반 처리가 더 직관적인 경우

## 마무리
Java NIO는 높은 성능과 확장성을 제공하는 강력한 I/O 프레임워크입니다. 특히 서버 애플리케이션 개발에서 많은 클라이언트 연결을 효율적으로 처리할 수 있게 해줍니다.
하지만 NIO는 기존 I/O보다 복잡하므로, 프로젝트의 요구사항을 신중히 고려하여 적절한 I/O 방식을 선택하는 것이 중요합니다.
최신 Java에서는 NIO를 기반으로 한 다양한 프레임워크(Netty, Undertow 등)들이 개발되어 있으니, 실제 프로덕션 환경에서는 이런 검증된 프레임워크 사용도 고려해볼 만합니다.
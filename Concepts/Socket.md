소켓(Socket)이란

소켓(Socket)은 **네트워크 통신의 양 끝점(endpoint)**이다.
프로그램이 네트워크를 통해 데이터를 주고받기 위해 사용하는 통신 창구라고 이해하면 된다.

간단히 말하면,

소켓 = IP 주소 + 포트 번호 + 통신 방식(TCP/UDP)

소켓의 기본 역할

네트워크 연결 생성

데이터 송수신

연결 종료 관리

웹 서버, 채팅, 게임, 실시간 알림 등
네트워크 통신이 필요한 거의 모든 프로그램의 기본 요소다.

소켓 통신의 기본 구조

소켓 통신은 항상 클라이언트-서버 구조를 가진다.

서버(Server)

특정 포트에서 대기

클라이언트의 연결 요청을 받음

요청에 응답

클라이언트(Client)

서버의 IP와 포트로 연결 요청

데이터 전송 및 수신

TCP 소켓 흐름

서버 소켓 생성

서버 바인딩(IP, 포트)

서버 리스닝

클라이언트 연결 요청

연결 수락

데이터 송수신

연결 종료

Python에서의 소켓
Python 소켓의 특징

표준 라이브러리 socket 제공

문법이 단순하고 직관적

네트워크 개념 학습용으로 매우 적합

Python TCP 서버 예제
import socket

server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server_socket.bind(("127.0.0.1", 8080))
server_socket.listen()

conn, addr = server_socket.accept()
print("connected:", addr)

data = conn.recv(1024)
print(data.decode())

conn.sendall(b"hello client")
conn.close()
server_socket.close()

Python TCP 클라이언트 예제
import socket

client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
client_socket.connect(("127.0.0.1", 8080))

client_socket.sendall(b"hello server")
data = client_socket.recv(1024)

print(data.decode())
client_socket.close()

Python 소켓의 특징 정리

함수 호출 중심의 절차적 구조

blocking 방식이 기본

멀티 클라이언트 처리는 threading 또는 async 사용

빠른 개발과 테스트에 적합

Java에서의 소켓
Java 소켓의 특징

객체지향적인 설계

TCP와 UDP를 클래스 레벨에서 명확히 구분

대규모 서버에 적합한 구조

Java TCP 서버 예제
import java.net.ServerSocket;
import java.net.Socket;
import java.io.*;

public class Server {
    public static void main(String[] args) throws Exception {
        ServerSocket serverSocket = new ServerSocket(8080);
        Socket socket = serverSocket.accept();

        BufferedReader in = new BufferedReader(
            new InputStreamReader(socket.getInputStream())
        );
        BufferedWriter out = new BufferedWriter(
            new OutputStreamWriter(socket.getOutputStream())
        );

        String msg = in.readLine();
        System.out.println(msg);

        out.write("hello client\n");
        out.flush();

        socket.close();
        serverSocket.close();
    }
}

Java TCP 클라이언트 예제
import java.net.Socket;
import java.io.*;

public class Client {
    public static void main(String[] args) throws Exception {
        Socket socket = new Socket("127.0.0.1", 8080);

        BufferedWriter out = new BufferedWriter(
            new OutputStreamWriter(socket.getOutputStream())
        );
        BufferedReader in = new BufferedReader(
            new InputStreamReader(socket.getInputStream())
        );

        out.write("hello server\n");
        out.flush();

        String response = in.readLine();
        System.out.println(response);

        socket.close();
    }
}

Java 소켓의 특징 정리

명확한 클래스 구조

예외 처리 필수

Stream 기반 입출력

멀티스레드 서버 구조에 적합

Python vs Java 소켓 비교
| 항목       | Python           | Java             |
| -------- | ---------------- | ---------------- |
| 제공 방식    | socket 모듈        | java.net 패키지     |
| 설계 스타일   | 절차적              | 객체지향             |
| 코드 길이    | 짧음               | 상대적으로 김          |
| 학습 난이도   | 낮음               | 중간               |
| 멀티 클라이언트 | threading, async | Thread, Executor |
| 실무 활용    | 프로토타입, 스크립트      | 대규모 서버           |

TCP와 UDP 간단 비교
TCP

연결 지향

데이터 순서 보장

신뢰성 높음

웹, 채팅, 파일 전송에 사용

UDP

비연결

빠르지만 신뢰성 낮음

순서 보장 없음

스트리밍, 게임에 사용

실무에서 소켓을 직접 쓰는 경우

요즘 실무에서는 다음과 같은 경우에 직접 소켓을 다룬다.

실시간 통신 서버

게임 서버

IoT 장비 통신

고성능 네트워크 서버

커스텀 프로토콜 구현

일반 웹 서비스에서는 보통
HTTP, WebSocket, gRPC 같은 상위 추상화 기술을 사용한다.

핵심 요약

소켓은 네트워크 통신의 가장 기본 단위

Python은 간결하고 학습용으로 좋음

Java는 구조적이고 대규모 서버에 적합

실무에서는 직접 소켓보다 상위 프로토콜을 더 많이 사용
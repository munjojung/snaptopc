C:\\python\_workspace\\snaptopc





\## 시스템 동작 구조 (System Architecture)



SnapToPC는 PeerJS와 WebRTC를 기반으로 여러 대의 휴대폰에서 촬영한 사진과 동영상을 한 대의 PC로 전송하는 웹 애플리케이션입니다.



동일 Wi-Fi 환경뿐 아니라 LTE/5G 외부망에서도 TURN 서버를 이용하여 연결할 수 있도록 지원합니다.



PC에서 TURN 설정을 활성화하면 접속 정보가 QR 코드에 자동으로 포함되므로, 휴대폰에서는 별도의 TURN 설정 없이 QR 코드 스캔만으로 연결을 시도할 수 있습니다.



\### 1. 전체 시스템 동작 흐름 (System Workflow)



```mermaid

flowchart TD

&#x20;   A\["PC · SnapToPC 실행"] --> B{"외부망 TURN 활성화?"}



&#x20;   B -->|OFF| C\["연결 코드만 포함한 QR 생성"]

&#x20;   B -->|ON| D\["TURN URL / Username / Credential"]



&#x20;   D --> E\["TURN 정보 Base64 인코딩"]

&#x20;   E --> F\["연결 코드 + TURN 정보 QR 생성"]



&#x20;   C --> G\["휴대폰 QR 코드 스캔"]

&#x20;   F --> G



&#x20;   G --> H{"TURN 정보 포함 여부"}



&#x20;   H -->|없음| I\["STUN 기반 연결 설정"]

&#x20;   H -->|있음| J\["TURN 정보 자동 디코딩"]



&#x20;   J --> K\["세션 메모리에 TURN 정보 저장"]

&#x20;   K --> L\["브라우저 주소창에서 TURN 정보 제거"]

&#x20;   L --> M\["STUN + TURN 연결 설정"]



&#x20;   I --> N\["PeerJS / WebRTC 연결 협상"]

&#x20;   M --> N



&#x20;   N --> O{"연결 경로 선택"}



&#x20;   O -->|직접 연결| P\["P2P 직접 전송"]

&#x20;   O -->|TURN 중계 연결| Q\["TURN 서버 중계 전송"]



&#x20;   P --> R\["PC에서 사진 및 동영상 수신"]

&#x20;   Q --> R



&#x20;   R --> S\["자동 저장 또는 수동 다운로드"]



&#x20;   classDef pc fill:#4338ca,color:#fff,stroke:#6366f1

&#x20;   classDef phone fill:#0369a1,color:#fff,stroke:#0ea5e9

&#x20;   classDef transfer fill:#047857,color:#fff,stroke:#10b981

&#x20;   classDef security fill:#92400e,color:#fff,stroke:#f59e0b



&#x20;   class A,B,C,D,E,F pc

&#x20;   class G,H,I,J,M phone

&#x20;   class K,L security

&#x20;   class N,O,P,Q,R,S transfer

```



\### 2. 시스템 아키텍처 (Network Architecture)



```mermaid

flowchart LR

&#x20;   subgraph Mobile\["휴대폰 · Mobile Devices"]

&#x20;       M1\["Smartphone 1"]

&#x20;       M2\["Smartphone 2"]

&#x20;       M3\["Smartphone N"]

&#x20;   end



&#x20;   subgraph Network\["Network Infrastructure"]

&#x20;       PS\["PeerJS Signaling Server"]

&#x20;       ST\["STUN Server"]

&#x20;       TU\["TURN Relay Server"]

&#x20;   end



&#x20;   subgraph PC\["PC · Receiver"]

&#x20;       P\["SnapToPC Receiver"]

&#x20;       S\["Local File Storage"]

&#x20;       P --> S

&#x20;   end



&#x20;   M1 -.->|Signaling| PS

&#x20;   M2 -.->|Signaling| PS

&#x20;   M3 -.->|Signaling| PS

&#x20;   PS -.-> P



&#x20;   M1 -.-> ST

&#x20;   M2 -.-> ST

&#x20;   M3 -.-> ST

&#x20;   P -.-> ST



&#x20;   M1 -->|Direct P2P| P

&#x20;   M2 -->|Direct P2P| P



&#x20;   M1 -->|Relay if needed| TU

&#x20;   M2 -->|Relay if needed| TU

&#x20;   M3 -->|Relay if needed| TU

&#x20;   TU --> P



&#x20;   classDef mobile fill:#0369a1,color:#fff,stroke:#0ea5e9

&#x20;   classDef network fill:#475569,color:#fff,stroke:#94a3b8

&#x20;   classDef receiver fill:#047857,color:#fff,stroke:#10b981



&#x20;   class M1,M2,M3 mobile

&#x20;   class PS,ST,TU network

&#x20;   class P,S receiver

```



\- PeerJS Signaling Server: WebRTC 연결 협상을 위한 시그널링 처리

\- STUN Server: NAT 환경에서 연결 후보 주소 확인

\- TURN Server: 직접 연결이 어려운 경우 미디어 데이터 중계

\- WebRTC DataChannel: 사진 및 동영상 파일 전송

\- IndexedDB: 휴대폰의 오프라인 전송 대기열 관리

\- Local Storage: 사용자 설정 및 언어 설정 보관



\### 3. TURN ON/OFF 동작 비교



| 구분 | TURN OFF | TURN ON |

|---|---|---|

| 동일 Wi-Fi 연결 | 지원 | 지원 |

| LTE/5G 외부망 연결 | 네트워크 환경에 따라 가능 | TURN 중계 지원 |

| STUN 서버 사용 | 사용 | 사용 |

| TURN 서버 사용 | 미사용 | 필요 시 사용 |

| QR 연결 코드 포함 | 포함 | 포함 |

| QR TURN URL 포함 | 미포함 | 포함 |

| QR Username 포함 | 미포함 | 포함 |

| QR Credential 포함 | 미포함 | 포함 |

| 휴대폰 TURN 수동 설정 | 불필요 | 불필요 |

| 직접 P2P 전송 | 지원 | 지원 |

| TURN 중계 전송 | 미지원 | 지원 |



TURN을 활성화하더라도 모든 데이터가 반드시 TURN 서버를

통과하는 것은 아닙니다.



WebRTC는 ICE 연결 협상을 통해 사용 가능한 연결 경로를

선택하며, 직접 연결이 어려운 경우 TURN 서버를 이용합니다.



\### 4. QR 코드 자동 설정 (Automatic TURN Configuration)



\#### TURN OFF



QR 코드에는 PC 연결 코드만 포함됩니다.



```text

https://munjojung.github.io/snaptopc/?room=Z9PZT

```



\#### TURN ON



QR 코드에는 연결 코드와 Base64로 인코딩된 TURN 접속 정보가

포함됩니다.



```text

https://munjojung.github.io/snaptopc/?room=Z9PZT#turn=TOKEN

```



TOKEN에는 다음 정보가 포함됩니다.



```json

{

&#x20; "u": "TURN Server URL",

&#x20; "n": "TURN Username",

&#x20; "c": "TURN Credential"

}

```



휴대폰에서 QR 코드를 스캔하면 다음 작업이 자동으로 수행됩니다.



1\. URL에서 연결 코드 추출

2\. TURN 접속 정보 추출 및 디코딩

3\. TURN 설정을 세션 메모리에 저장

4\. 브라우저 주소창에서 TURN 정보 제거

5\. STUN 및 TURN ICE 서버 설정

6\. PeerJS/WebRTC 연결 자동 시도

7\. 연결 성공 후 사진 및 동영상 전송



TURN 정보 제거 후 주소창에는 다음 주소만 남습니다.



```text

https://munjojung.github.io/snaptopc/?room=Z9PZT

```



\### 5. 연결 및 파일 전송 시퀀스 (Sequence Diagram)



```mermaid

sequenceDiagram

&#x20;   participant PC as PC Receiver

&#x20;   participant QR as QR Code

&#x20;   participant PH as Smartphone

&#x20;   participant PS as PeerJS Server

&#x20;   participant TS as TURN Server



&#x20;   PC->>PC: Configure and enable TURN

&#x20;   PC->>QR: Generate room code + TURN token

&#x20;   PH->>QR: Scan QR code

&#x20;   QR-->>PH: Room code + TURN token

&#x20;   PH->>PH: Decode TURN configuration

&#x20;   PH->>PH: Store configuration in memory

&#x20;   PH->>PH: Remove TURN token from URL



&#x20;   PC->>PS: Register receiver

&#x20;   PH->>PS: Request connection

&#x20;   PS-->>PC: Connection signaling

&#x20;   PS-->>PH: Connection signaling



&#x20;   Note over PC,PH: WebRTC ICE negotiation



&#x20;   alt Direct P2P connection selected

&#x20;       PH->>PC: Establish DataChannel

&#x20;       PH->>PC: Transfer photos and videos

&#x20;   else TURN relay connection selected

&#x20;       PH->>TS: Establish relayed connection

&#x20;       TS->>PC: Relay WebRTC traffic

&#x20;       PH->>TS: Transfer encrypted media data

&#x20;       TS->>PC: Relay media data

&#x20;   end



&#x20;   PC-->>PH: Transfer acknowledgement

&#x20;   PC->>PC: Save received media locally

```



\### 6. 외부망 LTE/5G 사용 방법



\#### PC 설정



1\. SnapToPC 웹사이트에 접속합니다.

2\. 노트북/PC 수신 모드를 선택합니다.

3\. 외부망(TURN) 설정창을 엽니다.

4\. 유효한 TURN Server URL, Username, Credential을 입력합니다.

5\. TURN을 활성화합니다.

6\. 저장 및 적용 버튼을 누릅니다.

7\. TURN 정보가 포함된 QR 코드가 생성됩니다.



\#### 휴대폰 설정



1\. 휴대폰의 Wi-Fi를 끕니다.

2\. LTE 또는 5G 모바일 데이터를 활성화합니다.

3\. PC 화면의 QR 코드를 스캔합니다.

4\. SnapToPC 휴대폰 모드가 실행됩니다.

5\. TURN 설정이 자동으로 적용됩니다.

6\. PC 연결이 완료될 때까지 기다립니다.

7\. 사진이나 동영상을 촬영하여 전송합니다.



외부망 접속을 위해서는 휴대폰에서 접근 가능한 공개 HTTPS

주소와 유효한 TURN 인증 정보가 필요합니다.



\### 7. 보안 및 개인정보 보호 (Security and Privacy)



\- TURN 인증 정보는 URL Query Parameter가 아닌

&#x20; URL Fragment(`#turn=TOKEN`)에 포함합니다.

\- URL Fragment는 일반적인 HTTP 요청을 통해 웹 서버에

&#x20; 전달되지 않습니다.

\- 휴대폰에서 TURN 정보를 읽은 후 주소창에서 제거합니다.

\- 휴대폰의 QR 기반 TURN 정보는 세션 메모리에만 보관합니다.

\- TURN을 비활성화하면 새로 생성되는 QR 코드에서

&#x20; TURN 정보가 제거됩니다.

\- 사진 및 동영상은 WebRTC DataChannel을 통해 암호화되어

&#x20; 전송됩니다.

\- TURN 서버는 WebRTC 트래픽을 중계하며, 별도의 미디어

&#x20; 저장 서버로 사용하지 않습니다.

\- 수신된 파일은 PC의 로컬 저장소에 저장할 수 있습니다.



> \[!WARNING]

> Base64는 암호화 기술이 아닙니다.

> QR 코드 또는 원본 접속 URL을 확보한 사람은 TURN 인증 정보를

> 확인할 수 있습니다.

>

> QR 코드는 신뢰할 수 있는 사용자에게만 공유하고,

> 유효기간이 짧은 TURN 인증 정보를 사용하는 것을 권장합니다.

>

> URL Fragment도 브라우저에서 실행되는 JavaScript가 접근할

> 수 있으므로 완전한 인증 정보 보호 수단은 아닙니다.



\### 8. 오프라인 전송 지원 (Offline Transfer)



```mermaid

flowchart TD

&#x20;   A\["사진 또는 동영상 촬영"] --> B{"PC 연결 상태"}



&#x20;   B -->|연결됨| C\["WebRTC 파일 전송"]

&#x20;   B -->|미연결| D\["IndexedDB 임시 저장"]



&#x20;   D --> E\["오프라인 전송 대기열"]

&#x20;   E --> F{"PC 연결 복구?"}



&#x20;   F -->|아니오| E

&#x20;   F -->|예| C



&#x20;   C --> G{"수신 확인 ACK"}

&#x20;   G -->|성공| H\["전송 완료 및 대기열 정리"]

&#x20;   G -->|실패| D



&#x20;   H --> I\["PC 로컬 파일 저장"]

```



휴대폰이 PC에 연결되지 않은 상태에서도 사진과 동영상을

촬영할 수 있습니다.



촬영한 미디어는 IndexedDB에 임시 저장되며, PC와의 연결이

복구되면 전송 대기열의 파일을 전송합니다.



\### 9. 한글 및 영어 지원 (Bilingual Support)



SnapToPC는 한국어와 영어 인터페이스를 지원합니다.



\- 초기 모드 선택 화면

\- PC 수신 화면

\- 휴대폰 촬영 및 전송 화면

\- 연결 상태 및 오류 메시지

\- QR 코드 및 TURN 설정 안내

\- PWA 설치 안내 모달

\- 파일 전송 진행률

\- 오프라인 전송 대기열

\- 토스트 알림 및 입력 안내



언어 설정은 브라우저에 저장되며 다음 접속 시 복원됩니다.



언어 변경은 기존 WebRTC 연결과 파일 전송 상태를

유지하도록 설계되어 있습니다.



\### 10. 운영상 참고 사항 (Operational Notes)



\- TURN 설정을 변경하면 PC의 PeerJS 연결이 재설정될 수 있습니다.

\- 기존 휴대폰 연결이 끊어질 경우 새로운 QR 코드로 재접속합니다.

\- 휴대폰을 새로고침하면 세션 메모리의 TURN 정보가 사라지므로

&#x20; QR 코드를 다시 스캔해야 합니다.

\- TURN 서버의 인증 정보가 만료되면 새로운 인증 정보를

&#x20; 발급받아 PC 설정을 갱신해야 합니다.

\- TURN 서버의 대역폭과 사용량에 따라 비용이 발생할 수 있습니다.

\- 실제 TURN 중계 여부는 WebRTC ICE Candidate Pair 정보를

&#x20; 통해 확인할 수 있습니다.



\### 11. 검증 상태 (Verification Status)



| 검증 항목 | 상태 |

|---|---|

| JavaScript 문법 검사 | PASS |

| TURN QR 생성 로직 수정 | 완료 |

| 휴대폰 TURN 자동 적용 로직 수정 | 완료 |

| 한글/영어 지원 | 구현 완료 |

| 실제 QR 스캔 테스트 | 검증 필요 |

| 실제 LTE/5G 연결 테스트 | 검증 필요 |

| TURN Relay Candidate 확인 | 검증 필요 |

| 사진 및 동영상 전송 회귀 테스트 | 검증 필요 |



실제 외부망 연결 성공 여부는 TURN 서버의 가용성,

인증 정보의 유효성 및 네트워크 환경에 따라 달라질 수 있습니다.




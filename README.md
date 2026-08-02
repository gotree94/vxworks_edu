# VxWorks 교육 커리큘럼 (순차적 전체 과정)

- 버전: v2.0
- 작성일: 2026-08-02
- 실습 플랫폼: AMD Kria KR260/KV260 (Zynq UltraScale+ MPSoC, ZU5EV/ZU7EV)
- 시뮬레이터: Wind River VxSim (VxWorks SDK 내장)

---

## 0. 이 문서의 학습 구조

이 과정은 **임베디드/RTOS를 처음 접하는 학습자**부터 **Kria 실보드에서 VxWorks로 실시간 제어 + AMP 하이브리드 시스템을 구축하는 수준**까지 하나의 순서로 이어진다.

```
Level 0  입문 기초 ──► Level 1  RTOS/VxWorks 코어 ──► Level 2  VxWorks 개발환경 심화
                                                          │
                          ┌───────────────────────────────┘
                          ▼
Level 3  임베디드 하드웨어·리눅스 기초 ──► Level 4  VxWorks 실보드 포팅 (Kria R5)
                          └───────────────────────────────┐
                                                          ▼
                              Level 5  AMP 하이브리드 & 캡스톤
```

| 레벨 | 주제 | 장소 | 핵심 산출물 |
|------|------|------|-------------|
| L0 | 임베디드·C 기초 | PC | 환경 설치 확인 |
| L1 | RTOS/VxWorks 코어 개념 | VxSim | 실습 코드 10종 |
| L2 | 개발환경·실행 심화 | VxSim (+보드) | DKM/RTP 애플리케이션 |
| L3 | 하드웨어·리눅스 기초 | Kria + Vivado | Linux 부팅, XSA 설계 |
| L4 | VxWorks 실보드 포팅 | Kria R5 | 부팅 이미지, 드라이버 |
| L5 | AMP 하이브리드·캡스톤 | Kria 전체 | 통합 데모 + 보고서 |

> 각 레벨은 **체크포인트를 통과해야 다음 레벨로** 넘어간다.

---

## 1. Level 0 — 입문 기초 (사전 준비)

**목표**: 임베디드 시스템과 C언어 기초를 다져 VxWorks 학습의 바탕을 만든다.
**선수 요건**: 없음 (운영체제 개념 정도)

| 순서 | 주제 | 내용 | 실습 |
|------|------|------|------|
| 0-1 | 임베디드 시스템 개론 | MCU vs SoC vs CPU, 레지스터/메모리맵/주변장치, 부팅이란 | 블록도 그리기 |
| 0-2 | RTOS vs GPOS | 하드/소프트 실시간, 결정론성, 커널 역할 | 사례 조사 (자동차/방산) |
| 0-3 | C언어 복습 (임베디드 관점) | 포인터/구조체/콜백, `volatile`, 비트 연산, 정적 vs 동적 | 미니 연습 10문 |
| 0-4 | 도구 설치 | Wind River Labs SDK(NCLA) 설치, VxSim 확인, Vivado 설치 | VxSim 첫 부팅 |
| 0-5 | VxWorks 개요 | VxWorks 7 구성, 커널 컴포넌트(`INCLUDE_*`), Workbench IDE 구조 | 예제 프로젝트 열기 |

**체크포인트**: VxSim에서 VxWorks가 부팅되고 `devs`, `version` 명령 실행 성공

---

## 2. Level 1 — RTOS/VxWorks 코어 개념 (VxSim)

**목표**: VxWorks의 태스크/스케줄링/동기화/메모리/ISR 개념을 코드로 익힌다.
**선수 요건**: Level 0

| 순서 | 주제 | 내용 | 실습 |
|------|------|------|------|
| 1-1 | 태스크 기초 | `taskSpawn`, `taskDelay`, 태스크 상태 전이, TCB/스택 | 태스크 2개 실행 |
| 1-2 | 스케줄링 | 우선순위 선점형, 라운드로빈, 우선순위 역전 | 스케줄 순서 관찰 |
| 1-3 | 세마포어 | 바이너리/카운팅/뮤텍스, `semGive/take`, 상속 | 생산자-소비자 |
| 1-4 | 이벤트·워치독 | `eventSend`, `wdCreate`, 데드락 | 데드락 재현 |
| 1-5 | 메시지 큐 | `msgQSend`, 파이프, 봉쇄/비봉쇄 | 데이터 전달 실습 |
| 1-6 | 메모리 관리 | `memPartCreate`, 단편화, 누수 | 메모리 사용량 측정 |
| 1-7 | 인터럽트·ISR | ISR 규칙, `intConnect`, 신호 | 버튼 ISR |
| 1-8 | POSIX API | POSIX 스레드/뮤텍스/큐 래핑 | POSIX 코드 작성 |
| 1-9 | 파일시스템 | dosFs/romfs/tffs, 볼륨 마운트 | 로그 파일 저장 |
| 1-10 | 종합 실습 | 동기화+메시지큐+ISR 통합 | 미니 데이터 로거 |

**체크포인트**: 종합 실습 코드가 VxSim에서 안정 동작

---

## 3. Level 2 — VxWorks 개발환경 & 실행 심화 (VxSim)

**목표**: 프로젝트 구성, 배포 단위, 디버깅, 네트워크 등 실제 개발 워크플로우를 익힌다.
**선수 요건**: Level 1

| 순서 | 주제 | 내용 | 실습 |
|------|------|------|------|
| 2-1 | 프로젝트 모델 | 커널 이미지(IP), DKM, RTP 차이와 용도 | 각 방식으로 빌드 |
| 2-2 | DKM 개발 | Downloadable Kernel Module, 빌드→로드/언로드 | DKM 실습 |
| 2-3 | RTP 개발 | User-mode RTP, `rtpSpawn`, 메모리 보호 | RTP 실습 |
| 2-4 | VxWorks 셸 | C 셸 명령(`devs`, `taskShow`, `i`, `d`), 스크립트 | 원격 셸 제어 |
| 2-5 | 디버깅 | Target Server, WDB, 브레이크포인트, `wdb` | 버그 수정 실습 |
| 2-6 | 네트워킹 | IP/소켓, TCP/UDP 통신, 보드 간 통신 | VxSim↔PC 통신 |
| 2-7 | 부팅 과정 | 부트 ROM, VIP(VxWorks Image Protocol), bootline | 부트 시퀀스 분석 |
| 2-8 | 실시간 성능 측정 | 타임스탬프, `taskLock`/`intLock` 영향, 워치독 | 지연 측정 리포트 |

**체크포인트**: TCP 통신 + DKM/RTP 구조가 섞인 애플리케이션 완성

---

## 4. Level 3 — 임베디드 하드웨어·리눅스 기초 (Kria 준비)

**목표**: 소프트웨어 개발자가 하드웨어(Zynq MPSoC)와 리눅스 생태계를 이해해 실보드로 진입한다.
**선수 요건**: Level 2 (Level 3부터는 Kria 보드·Vivado 필요)

| 순서 | 주제 | 내용 | 실습 |
|------|------|------|------|
| 3-1 | Zynq UltraScale+ 아키텍처 | PS(A53/R5) vs PL, 클럭/메모리/인터커넥트 | PS 구성 트리 분석 |
| 3-2 | ARM 코어 이해 | A53(고성능) vs R5(실시간·Lockstep) 역할 | 코어 비교 정리 |
| 3-3 | Vivado 하드웨어 설계 | PS 설정, UART/GPIO/BRAM IP, XSA export | XSA 생성 |
| 3-4 | 리눅스 부팅 기본 | FSBL→ATF→U-Boot→커널 흐름, device tree | 부팅 로그 읽기 |
| 3-5 | Kria 표준 사용 | Ubuntu 부팅, xmutil, 사전 빌드 앱 | KR260/KV260 GPIO 제어 |
| 3-6 | (대안 경로) 표준 평가보드 | ZCU102/ZCU104 공식 VxWorks BSP 확인 | 리서치+환경 점검 |

**체크포인트**: Kria에서 리눅스가 부팅되고 주변장치 제어 성공, XSA 플랫폼 완성

---

## 5. Level 4 — VxWorks 실보드 포팅 (Kria R5)

**목표**: Kria의 R5 코어에 VxWorks를 올려 실시간 태스크를 실행한다. 이 과정의 가장 어려운 구간.
**선수 요건**: Level 3

> **주의**: Kria 전용 공식 Wind River BSP는 없음. 커스텀 BSP 작업이 필요하며,
> 난이도가 높아 ZCU102/ZCU104(공식 BSP) 경로를 나란히 두고 비교 학습한다.

| 순서 | 주제 | 내용 | 실습 |
|------|------|------|------|
| 4-1 | BSP 개념 | BSP 구성(`config.h`, sysLib, 시작 코드), 론치 흐름 | BSP 구조 분석 |
| 4-2 | BSP 생성 | Workbench에서 XSA 가져오기, Kria 커스텀 BSP 구성 | BSP 빌드 성공 |
| 4-3 | 부트체인 1 | Xilinx FSBL/PMU FW, `boot.bif`, bootgen | 부팅 이미지 생성 |
| 4-4 | 부트체인 2 | R5 실행 경로, VxWorks 이미지 로딩 | R5에서 VxWorks 부팅 |
| 4-5 | 타깃 배포·디버깅 | Target Server, FTP/TFTP, WDB 연동 | 원격 디버깅 |
| 4-6 | 디바이스 드라이버 | GPIO/UART/타이머, `sysDrv`, 인터럽트 연결 | LED/버튼 제어 |
| 4-7 | 실시간 제어 태스크 | 폐루프 주기 보장, µs 정밀도 측정 | 1kHz 제어 루프 |
| 4-8 | SMP | R5 듀얼코어 SMP, `smpShow`, 코어 친화도 | 멀티코어 실험 |

**체크포인트**: Kria R5에서 VxWorks로 주기적 실시간 제어 태스크 동작 + 시리얼 로그

---

## 6. Level 5 — AMP 하이브리드 & 캡스톤 (Kria 전체)

**목표**: A53(리눅스) + R5(VxWorks) + PL(FPGA)을 하나의 시스템으로 통합한다.
**선수 요건**: Level 4

```
[PL]  센서/영상 프리프로세싱 (Vivado 로직)
   │
   ▼
[R5]  VxWorks 실시간 제어 (모터·폐루프)
   │  OpenAMP (RPMSG, 공유 메모리)
   ▼
[A53] Linux — 비전/AI 추론, ROS2, xmutil (Kria 표준)
```

| 순서 | 주제 | 내용 | 실습 |
|------|------|------|------|
| 5-1 | SMP vs AMP | 구성 비교, 선택 기준, 하이퍼바이저 소개 | 구성 설계 |
| 5-2 | OpenAMP 기반 통신 | RPMSG, 공유 메모리, 리눅스 remoteproc에서 R5 VxWorks 로드 | A53↔R5 메시지 |
| 5-3 | DMA·인터럽트 파이프라인 | PL DMA와 R5 ISR 연동 | 영상 프레임 전송 |
| 5-4 | 종단간 지연 측정 | A53→R5 왕복 지연, 실시간 보장 검증 | 지연 리포트 |
| 5-5 | 안전·인증 (심화) | VxWorks Safety Profile, ISO 26262/IEC 61508, TSN | 인증 요구사항 정리 |
| 5-6 | 확장 기술 | VxWorks의 C++17/Python/Rust, ROS 2 연동, 컨테이너 | 데모 조사 |
| 5-7 | 캡스톤 설계 | "방산 UAS/AMR 축소판" 요구사항 정의 | 설계 문서 |
| 5-8 | 캡스톤 구현 | PL 전처리→R5 제어→A53 비전 파이프라인 구현 | 통합 데모 |
| 5-9 | 캡스톤 발표 | 성능 측정, AMP 부트체인 문서, 회고 | 최종 발표 |

**체크포인트**: 장애물 검출(A53) → 경로 명령(OpenAMP) → 실시간 모터 제어(R5) 동작

---

## 7. 전체 일정 (기본형)

| 레벨 | 회차 | 누적 | 비고 |
|------|------|------|------|
| L0 | 5 | 5 | PC만으로 가능 |
| L1 | 10 | 15 | VxSim만으로 가능 |
| L2 | 8 | 23 | VxSim (+선택 보드) |
| L3 | 6 | 29 | Kria + Vivado 필요 |
| L4 | 8 | 37 | 커스텀 BSP 구간 (최난이도) |
| L5 | 9 | 46 | 캡스톤 포함 |

> 원하는 최종 수준에 따라 중간에서 수료 가능:
> - 이론/입문만 → L1 수료
> - 실무 VxWorks 개발자 → L2 수료
> - 임베디드 시스템 엔지니어 → L3 수료
> - 실시간 시스템 전문가 → L4 수료
> - 풀스택(AMP/인증/캡스톤) → L5 수료

---

## 8. 실습 환경 구성 요약

```
[필수] Wind River Labs VxWorks SDK (NCLA, 무료) → VxSim 시뮬레이터 포함
[필수] AMD Vivado (ML 표준판)                     → L3부터
[필수] Kria KR260/KV260 보드                      → L3부터
[대안] ZCU102/ZCU104 (공식 VxWorks BSP)          → L4 난이도 완화
[선택] Wind River University Program (정식 신청)  → 정식 지원·라이선스
```

---

## 9. 리스크 및 대안

| 리스크 | 대응 |
|--------|------|
| Kria 전용 공식 BSP 부재 | L4 커스텀 BSP 실습에 포함 + ZCU102/ZCU104 대안 병행 |
| 부트체인(FSBL/boot.bif) 난이도 높음 | L3에서 리눅스 부팅 로그 분석으로 선행 학습 |
| 라이선스 비용 | Labs SDK(NCLA) 무료 경로로 시작, 필요시 University Program |
| AMP 구축 복잡도 | 단일 코어(A53/R5) 완성 → 멀티코어로 확장 순서 |
| 보드 수급 | VxSim만으로 L1~L2 전체 가능 (보드 없이 시작 가능) |

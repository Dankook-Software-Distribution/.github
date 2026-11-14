<div align="center">

<h1>RX64</h1>

<p><b>xv6-riscv를 x86-64로 두 번 포팅하고, 커널이 실행한 모든 명령어를 기록해 디버거처럼 되감아 본다</b></p>

![C](https://img.shields.io/badge/C-A8B9CC?style=flat-square&logo=c&logoColor=black)
![Assembly](https://img.shields.io/badge/GAS_Assembly-6E4C13?style=flat-square)
![x86-64](https://img.shields.io/badge/x86--64-0071C5?style=flat-square&logo=intel&logoColor=white)
![QEMU](https://img.shields.io/badge/QEMU-FF6600?style=flat-square&logo=qemu&logoColor=white)
![Java](https://img.shields.io/badge/Java_25-007396?style=flat-square&logo=openjdk&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring_Boot-6DB33F?style=flat-square&logo=springboot&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=typescript&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)

</div>

---

## 팀원

<table>
<tr>
  <th align="center">이름</th>
  <th align="center">학번</th>
  <th align="center">GitHub</th>
  <th>담당</th>
</tr>
<tr>
  <td align="center" nowrap>김보겸 (팀장)</td>
  <td align="center">32210**7</td>
  <td align="center"><a href="https://github.com/bogamie">@bogamie</a></td>
  <td><b>rx64-x86-style</b> 전체: Higher-half 커널 · 스케줄러와 타이머 선점 · 유저 페이지 테이블 · <code>int $64</code> 유저 모드 진입/복귀 · lazy 유저 페이지 · SMP 부팅 · q35 + virtio-blk-pci 디스크<br><b>rx64-viz</b> 전체: QEMU TCG 트레이스 플러그인 · Spring Boot 인덱서/서버 · 웹 UI</td>
</tr>
<tr>
  <td align="center" nowrap>최승원</td>
  <td align="center">32214**2</td>
  <td align="center"><a href="https://github.com/SeungwonChoi-kr">@SeungwonChoi-kr</a></td>
  <td><b>rx64-riscv-style</b>: 부트(PVH 진입 · long mode 전환 · 링커 스크립트 · Makefile) · UART(포트 I/O) · LAPIC 타이머와 IOAPIC 인터럽트 · PCI와 virtio-pci 디스크 드라이버 · 유저 모드(trampoline <code>uservec</code>/<code>userret</code> · <code>swapgs</code> · CPUENTRY의 GDT/TSS/유저 IDT) · 시스템 콜 ABI와 trapframe · usertests x86 주소 조정 · 소스 구조 정리 · riscv↔x86 대응 문서</td>
</tr>
<tr>
  <td align="center" nowrap>안태현</td>
  <td align="center">32212**8</td>
  <td align="center"><a href="https://github.com/Kcits970">@Kcits970</a></td>
  <td><b>rx64-riscv-style</b>: 4단계 페이징(<code>vm.c</code> · CR3 · NX) · 커널 트랩(IDT · CPU별 GDT/TSS · IST · <code>kernelvec</code>) · 컨텍스트 스위치(<code>swtch.S</code> · GS base로 <code>cpuid</code>/<code>mycpu</code>) · SMP(APENTRY · INIT/SIPI로 AP 기동) · PR 리뷰</td>
</tr>
</table>

---

## 어떤 프로젝트인가요?

같은 커널을 같은 하드웨어 위에서 **두 가지 설계**로 만들면 무엇이 달라질까요?

RX64는 MIT의 교육용 운영체제 xv6-riscv를 x86-64로 두 번 포팅했습니다. 하나는 x86에서 흔히 쓰는 설계(higher-half 커널, 커널 스택 위의 trapframe)로, 다른 하나는 xv6-riscv의 설계(유저/커널 페이지 테이블 분리, trampoline)를 그대로 유지한 채로 옮겼습니다. 두 커널은 같은 시스템 콜, 같은 유저 프로그램, 같은 파일 시스템을 갖고 있어서 아키텍처에 의존하는 부분만 나란히 비교할 수 있습니다.

그리고 교과서의 그림이 아니라 **커널이 실제로 한 일**을 보여 주기 위해 rx64-viz를 만들었습니다. xv6 셸에 명령을 입력하면 QEMU 플러그인이 커널과 유저 프로그램이 실행한 모든 명령어(바뀐 레지스터, 메모리 쓰기, 인터럽트)를 기록하고, 브라우저에서 그 기록을 C 소스 줄 단위로 앞뒤로 이동하며 레지스터 · 스택 · 페이지 테이블 · 프로세스 테이블 · 시스템 콜을 확인할 수 있습니다. 화면에 나오는 값은 모두 실제로 기록된 값입니다.

---

## 시연 영상

<!-- 영상 업로드 후 VIDEO_ID를 바꾸고 이 주석을 지우세요.
<a href="https://youtu.be/VIDEO_ID">
<img src="https://img.youtube.com/vi/VIDEO_ID/maxresdefault.jpg" alt="RX64 시연 영상" width="640">
</a>
-->

> 준비 중입니다.

---

## 레포지토리

| 레포지토리 | 기본 브랜치 | 내용 |
|---|---|---|
| [**rx64-x86-style**](https://github.com/Dankook-Software-Distribution/rx64-x86-style) | `x86_64/main` | x86 관례를 따른 포팅. 커널은 `0xFFFFFFFF80000000`의 higher-half에 있고, 트랩이 오면 trapframe이 커널 스택에 쌓입니다. |
| [**rx64-riscv-style**](https://github.com/Dankook-Software-Distribution/rx64-riscv-style) | `x86-64/main` | xv6-riscv의 설계를 그대로 유지한 포팅. 아키텍처에 의존하는 부분만 바꾸고 이유를 주석으로 남겼습니다. 대응 관계는 [`doc/riscv-x86.md`](https://github.com/Dankook-Software-Distribution/rx64-riscv-style/blob/x86-64/main/doc/riscv-x86.md)에 정리했습니다. |
| [**rx64-viz**](https://github.com/Dankook-Software-Distribution/rx64-viz) | `main` | 두 커널의 실행을 기록하고 브라우저에서 디버거처럼 보여 주는 시각화 도구. 두 커널을 서브모듈로 포함합니다. |

원본 xv6-riscv는 두 커널 레포의 `riscv` 브랜치에 있습니다.

---

## 두 커널 비교

| | rx64-x86-style | rx64-riscv-style |
|---|---|---|
| 커널 주소 | Higher-half (`0xFFFFFFFF80000000`) | Identity 매핑 (VA = PA, `0x100000`부터) |
| 페이지 테이블 | 유저 주소 공간의 상위 절반에 커널을 매핑 | 커널과 유저 페이지 테이블이 따로 있음. 유저 공간에는 커널을 매핑하지 않음 |
| 트랩 진입 | CPU가 TSS의 `rsp0`(커널 스택)으로 바꾸고 trapframe을 커널 스택에 저장 | 유저와 커널 양쪽 최상단의 **trampoline** 페이지로 들어가 CR3를 바꾸고, `TRAPFRAME` 페이지에 저장 |
| GDT / TSS / IDT | 커널 메모리에 둠(CPU별 GDT/TSS, 모든 CPU가 함께 쓰는 IDT 하나) | 유저 페이지 테이블로도 보여야 해서 **CPUENTRY** 페이지에 둠. 트랩마다 유저용/커널용 IDT를 바꿈 |

**공통:** QEMU q35, 4단계 페이징, `int $64` 시스템 콜, LAPIC 타이머 + IOAPIC, virtio-blk-pci 디스크, SMP(기본 CPU 3개), xv6 usertests

---

## rx64-viz 주요 기능

| | |
|:---:|---|
| **명령 단위 기록** | 셸에 입력한 명령 하나가 기록 하나. 각 커널의 부팅도 진입점(`_entry`)부터 첫 프롬프트까지 기록 |
| **디버거식 이동** | C 줄 / 명령어 단위로 Step over · into · out, **되감기(Step back)**, 다음/이전 트랩, 루프 건너뛰기, 커서 위치까지 실행 |
| **Syscalls** | 모든 시스템 콜과 예외를 strace 형식으로(`write(1, "hi", 2) = 2`). sleep했는지, 누가 깨웠는지, 처리한 커널 함수로 이동 |
| **Changes** | 호출 하나가 바꾼 것: `proc[]` 필드, 열린 파일과 파이프, free list에서 빠지거나 돌아온 프레임, 페이지 테이블. 각 변경을 만든 store로 이동 |
| **Memory** | CR3 주소 공간, 가상 주소 하나의 4단계 page walk, 물리 프레임 전체의 용도(실제 `kmem.freelist` 기준) |
| **Timeline · Processes** | 스레드별 실행 구간, sleep/runnable 상태와 wakeup, 프로세스 테이블 |
| **Stack · Variables · CPU** | 슬롯별 스택(trapframe 필드와 누가 썼는지), DWARF 기반 지역 변수, GDT/TSS/IDT |

---

## 빠른 시작

Docker(BuildKit 포함)만 있으면 됩니다. Ubuntu의 `docker.io` 패키지를 쓴다면 `docker-buildx`도 설치해야 합니다(`sudo apt install docker-buildx`).

```sh
git clone --recurse-submodules https://github.com/Dankook-Software-Distribution/rx64-viz.git
cd rx64-viz
docker build -t rxviz .
docker run --rm -p 127.0.0.1:8080:8080 -v "$PWD/runs/data:/data" rxviz
```

http://localhost:8080 을 열고 Console에 `echo hi`, `lazytouch`(페이지 폴트 3번), `pipewait`(파이프 sleep/wakeup)를 입력해 보세요. 첫 실행 때는 두 커널의 부팅을 기록하고 인덱싱하느라 10분 안팎이 걸리고(컴퓨터 성능에 따라 다름), 그동안에도 셸은 쓸 수 있습니다. 다음 실행부터는 1분 안쪽입니다.

커널만 실행하려면 각 커널 레포에서 `make qemu`를 실행합니다(x86-64 gcc와 QEMU 7.2 이상 필요).

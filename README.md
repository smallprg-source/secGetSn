# PC 통합 정보 확인 도구

Windows 및 Windows PE 환경에서 PC의 식별 정보, 주요 부품, 네트워크 설정과 온도 센서를 한 번에 확인하는 PyQt5 기반 점검 도구입니다.

삼성, Dell, HP 등 완제품 PC와 조립 PC를 지원하며 데스크톱과 노트북에서 사용할 수 있습니다. PC 입출고 관리, 자산 조사, 유지보수 및 장애 점검 과정에서 수집한 정보를 CSV로 저장할 수 있습니다.

## 주요 기능

### PC 식별 정보

- PC 모델명 후보 자동 조회 및 선택
- BIOS, 시스템 제품, 베이스보드 기반 제조번호 조회
- 시스템 UUID 조회
- 사용자명과 비고 입력
- 선택한 정보 클립보드 복사
- `settingPC.csv` 누적 저장
- 동일한 제조번호의 기존 등록 여부 확인

### 시스템 및 부품 정보

- 일반 Windows와 Windows PE 환경 구분
- OS 제품명, 버전 및 빌드 번호
- PC 제조사, 모델 및 UUID
- CPU 모델, 코어·스레드 및 최대 클럭
- 메인보드 제조사, 모델, 버전 및 시리얼
- BIOS 제조사, 버전, 날짜 및 시리얼
- 총 메모리 용량
- 메모리 슬롯별 모델, 제조사, 용량, 속도 및 시리얼
- 그래픽 어댑터 모델과 드라이버
- 모든 HDD, SSD, NVMe의 모델, 시리얼, 용량, 연결 방식 및 펌웨어

### 온도 확인

- CPU, GPU, 저장장치, 메인보드 및 지원되는 RAM 센서 조회
- 2초 간격 자동 갱신
- 부품별 기준 센서에 `★` 표시
- 기준 센서 행의 상태를 배경색으로 구분
  - 녹색: 정상
  - 주황색: 경고
  - 붉은색: 기준 초과
- LibreHardwareMonitor를 우선 사용하고 WMI, ACPI, NVIDIA-SMI 방식으로 자동 전환

기본 온도 기준은 다음과 같습니다.

| 부품 | 정상 | 경고 | 초과 |
|---|---:|---:|---:|
| CPU | 80°C 미만 | 80°C 이상 | 90°C 이상 |
| GPU | 80°C 미만 | 80°C 이상 | 90°C 이상 |
| 저장장치 | 50°C 미만 | 50°C 이상 | 60°C 이상 |
| RAM | 70°C 미만 | 70°C 이상 | 85°C 이상 |
| 메인보드 및 시스템 | 70°C 미만 | 70°C 이상 | 85°C 이상 |

온도 기준은 일반적인 점검을 위한 기본값입니다. 실제 허용 온도는 부품 제조사의 사양을 우선해야 합니다.

### 네트워크 정보

- 어댑터별 현재 연결 상태
- 연결된 어댑터 행 녹색 표시
- 자동 DHCP 및 수동 고정 IP 구분
- IPv4 및 IPv6 주소
- 서브넷
- 기본 게이트웨이
- DNS 서버
- DHCP 서버
- MAC 주소
- 연결 속도
- Windows 인터넷 연결 감지 상태
- 컬럼 순서 드래그 이동 및 너비 조절

### 정보 저장 및 관리 도구

- 부품 및 네트워크 정보를 UTF-8 CSV로 저장
- 저장한 CSV를 Excel 또는 기본 연결 프로그램으로 열기
- 장치관리자 열기
- 네트워크 설정 열기
- 디스크 관리 열기
- 메인 창과 정보 창 최대화 및 최소화 지원

## 실행 환경

- Windows 10 또는 Windows 11
- Windows PE x64
- Python 3.10
- 관리자 권한 권장

일부 CPU, 메인보드, 저장장치 센서는 관리자 권한이 없으면 조회되지 않거나 `0°C`와 같은 잘못된 값을 반환할 수 있습니다. 프로그램은 `0°C` 값을 유효하지 않은 센서값으로 처리합니다.

## 설치

프로젝트 폴더에서 다음 명령을 실행합니다.

```powershell
py -3.10 -m pip install -r requirements.txt
```

주요 Python 의존성은 다음과 같습니다.

- PyQt5
- pywin32
- pythonnet

온도 조회에 필요한 LibreHardwareMonitor DLL과 라이선스 파일은 `third_party/LibreHardwareMonitor`에 포함되어 있습니다.

## 소스 실행

관리자 권한으로 PowerShell을 실행한 후 다음 명령을 사용합니다.

```powershell
py -3.10 secGetSn.py
```

관리자 권한이 없거나 LibreHardwareMonitor를 사용할 수 없으면 확인 가능한 WMI, ACPI 또는 NVIDIA-SMI 센서만 표시됩니다.

## EXE 빌드

먼저 빌드 환경에 모든 의존성이 설치되어 있어야 합니다.

```powershell
py -3.10 -m pip install -r requirements.txt
```

온도 센서 접근을 위해 관리자 권한을 요청하는 단일 EXE 빌드를 권장합니다.

```powershell
py -3.10 -m PyInstaller --clean --noconfirm --onefile --windowed --uac-admin --icon=sec_icon.ico --add-data "sec_icon.ico;." --add-data "third_party/LibreHardwareMonitor;third_party/LibreHardwareMonitor" --hidden-import=clr --hidden-import=pythonnet --name secGetSn secGetSn.py
```

빌드 결과는 다음 위치에 생성됩니다.

```text
dist\secGetSn.exe
```

프로젝트에 포함된 `secGetSn.spec`을 이용할 수도 있습니다.

```powershell
py -3.10 -m PyInstaller --clean --noconfirm secGetSn.spec
```

현재 spec 설정은 관리자 권한을 강제로 요청하지 않습니다. 더 많은 센서를 조회하려면 직접 빌드 명령의 `--uac-admin` 사용을 권장합니다.

## Windows PE 사용 조건

Windows PE는 일반 Windows보다 사용할 수 있는 시스템 구성요소와 드라이버가 제한적입니다.

최소한 다음 조건을 확인해야 합니다.

- `WinPE-WMI` 선택 구성요소
- LibreHardwareMonitor 직접 연동을 위한 `WinPE-NetFX`
- 대상 PC의 칩셋, 스토리지 및 네트워크 드라이버
- 쓰기 가능한 임시 폴더

WinPE 이미지 구성에 따라 다음 기능이 제한될 수 있습니다.

- CPU 및 메인보드 온도
- SMART 기반 HDD, SSD 및 NVMe 온도
- 무선 네트워크
- Windows 설정 앱
- 장치관리자와 디스크 관리 MMC

지원되지 않는 구성요소를 실행하면 프로그램이 종료되지 않고 안내 메시지를 표시합니다.

## 저장 파일

### PC 등록 정보

메인 창의 저장 기능은 실행 폴더에 `settingPC.csv`를 생성하고 다음 정보를 누적합니다.

- 번호
- 일시
- 사용자명
- 모델코드
- PC 제조번호
- 비고

### 부품 및 네트워크 정보

부품/네트워크 창의 정보 저장 기능은 다음과 같은 정규화된 CSV 형식으로 저장합니다.

```text
영역,대상,항목,값
부품,CPU,모델,AMD Ryzen ...
네트워크,이더넷,IP 주소,192.168.0.10
```

CSV는 `UTF-8 with BOM` 형식이므로 한글 Windows와 Excel에서 바로 열 수 있습니다.

## 센서 관련 참고 사항

- 모든 PC가 모든 온도 센서를 공개하는 것은 아닙니다.
- 노트북은 제조사 전용 Embedded Controller 때문에 일부 센서가 표시되지 않을 수 있습니다.
- RAM 온도는 장착된 DIMM에 온도 센서가 있을 때만 표시됩니다.
- NVMe는 대표 온도와 여러 내부 센서를 제공할 수 있습니다.
- 저장장치는 `Temperature` 또는 `Composite Temperature`를 대표 온도로 사용합니다.
- 추가 센서의 번호와 실제 측정 위치는 제조사마다 다를 수 있습니다.
- RAID 또는 일부 스토리지 드라이버는 SMART 정보 전달을 차단할 수 있습니다.

## 프로젝트 구성

```text
secGetSn.py                         메인 프로그램
secGetSn.spec                       PyInstaller 설정
requirements.txt                    Python 의존성
sec_icon.ico                        프로그램 아이콘
third_party/LibreHardwareMonitor/   온도 센서 라이브러리 및 라이선스
```

## 오픈소스 라이선스

온도 조회에는 [LibreHardwareMonitor](https://github.com/LibreHardwareMonitor/LibreHardwareMonitor) 0.9.6을 사용합니다.

- LibreHardwareMonitor 및 관련 구성요소: MPL 2.0
- HidSharp: Apache License 2.0
- Microsoft .NET 호환 구성요소: MIT License

라이선스 전문, 제3자 고지 및 소스 위치는 다음 폴더에 포함되어 있습니다.

```text
third_party/LibreHardwareMonitor/
```

LibreHardwareMonitor 관련 바이너리는 수정하지 않은 별도 파일로 배포됩니다.

## 주의 사항

이 프로그램이 표시하는 정보는 WMI, 펌웨어, 장치 드라이버 및 센서 라이브러리에서 제공받은 값입니다. 중요한 장비의 온도 또는 장애 여부를 판단할 때는 부품 제조사의 공식 진단 도구와 사양을 함께 확인하시기 바랍니다.

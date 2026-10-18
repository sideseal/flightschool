# 실제 검증 기록

검증일: 2026-10-03. Linux x86_64에서 공식 elan 배포본으로 검사했습니다.

```text
Lean version 4.34.1
commit 5045d0056413266e57c625dcd7c365b10e377c52
Lake version 5.0.0-src+5045d00 (Lean version 4.34.1)
```

| 검사 | 결과 |
|---|---|
| `lake build` | 성공, 모든 수업·연습·해답·프로젝트·감사 모듈 포함 |
| `lake exe flightschool` | 성공, 여섯 시나리오의 이벤트별 큐·노드 상태 출력 |
| `#guard` | 정상·데이터 손실·ACK 손실·데이터 중복·ACK 중복·오래된 ACK 결과 일치 |
| 경계 검사 | idle timeout/빈 큐 전달 거절, waiting start 거절, 중복 수신 카운터·오래된 ACK 중간값 유지 |
| `python3 scripts/audit.py` | Lean 소스 17개에서 sorry/admit/사용자 공리/unsafe/native_decide 없음 |
| `lake env lean scripts/Audit.lean` | 핵심 정리 12개의 의존 공리 출력, sorryAx 없음 |
| `bash scripts/check.sh` | 빌드·실행·소스 및 증명 감사 성공 |

일부 정리는 Lean 표준 논리의 `propext`, `Classical.choice`, `Quot.sound`에 의존합니다. 사용자 정의 공리나 미완성 증명을 추가하지 않았습니다. `omega`가 만든 산술 증명도 커널 검사를 거칩니다.

## 개발 컨테이너에서 재실행

`.devcontainer`는 컨테이너를 만들 때 `/workspace/.elan`에 elan과 고정된 Lean 배포본을 설치합니다. 이 폴더와 `.lake/`는 Git에서 제외합니다. 컨테이너의 터미널에서 다음처럼 실행합니다.

```sh
bash scripts/check.sh
```

일반 사용자는 README의 표준 elan 설치 절차를 사용하면 됩니다. 저장소 소스와 `lean-toolchain`만 있으면 빌드할 수 있으며 `.elan/` 배포본을 저장소에 포함할 필요가 없습니다.

## 직접 검증하지 않은 항목

VS Code GUI 상호작용, Windows/macOS에서의 설치·빌드, GitHub 원격 CI 실행은 이 Linux 세션에서 직접 확인하지 않았습니다. CI 설정은 제공했지만 원격 실행 결과는 확인하지 않았습니다. 개발 컨테이너(`.devcontainer`)에서는 2026-10-18에 Lean 4.34.1 설치와 `bash scripts/check.sh` 통과를 확인했습니다. 핵심 Lean 빌드·실행·증명에는 미검증 항목이 남아 있지 않습니다. 실제 네트워크 구현이나 liveness 증명은 교육 모델의 범위 밖입니다.

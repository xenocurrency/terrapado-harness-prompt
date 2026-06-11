# 🏛️ Terrapado Harness Prompt Hub

> **디지털 수도원(Terrapado)** — 21세기 도파민 중독에 저항하는 침묵의 성소

## 개요

이 허브는 **Terrapado** 감정 기록 앱 개발을 위한 프롬프트 엔지니어링 구조입니다.
AI 에이전트(OpenCode, Claude, GPT 등)를 활용하여 앱을 개발할 때 일관된 아키텍처와 디자인을 유지하도록 돕습니다.

## 디렉토리 구조

```
harness/
├── prompts/                        # 실제 프롬프트 파일들
│   ├── system/                     # 시스템 프롬프트 (페르소나 정의)
│   │   └── terrapado-pm-persona.md
│   ├── tasks/                      # 구체적인 과업(Task) 프롬프트
│   │   └── emotion-orb-animation.md
│   └── templates/                  # 재사용 가능한 프롬프트 템플릿
│       └── screen-transition-template.md
├── contexts/                       # 프롬프트가 참조할 지식/데이터
│   └── terrapado-design-system.md
├── evals/                          # 프롬프트 성능 테스트 케이스
│   └── emotion-input-eval.md
├── logs/                           # AI 에이전트 실행 기록 및 피드백
│   └── 2026-06-11_orb-animation-issue.md
├── config.yaml                     # 모델 설정 (Temperature, Top-p, Model Name 등)
└── Terrapado-Harness-Prompt.md     # 🎯 메인 하네스 프롬프트 (실행 진입점)
```

## 사용 방법

1. [`Terrapado-Harness-Prompt.md`](./Terrapado-Harness-Prompt.md)를 AI 에이전트에 입력으로 전달
2. 에이전트가 `prompts/`, `contexts/`, `evals/` 하위 파일들을 참조하여 작업 수행
3. 작업 결과와 발견된 이슈를 `logs/`에 기록
4. `evals/`의 테스트 케이스로 결과물 검증

## 버전 히스토리

| 버전 | 날짜 | 변경 사항 |
|------|------|-----------|
| 1.0.0 | 2026-06-11 | 최초 생성 — Terrapado Ver.0.99 기준 |

## 라이선스

이 프로젝트는 개인 개발 목적으로 자유롭게 사용할 수 있습니다.

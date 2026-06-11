# 컨텍스트: Terrapado 디자인 시스템 & 기술 참조

> **파일 위치**: `contexts/terrapado-design-system.md`
> **목적**: AI 에이전트가 일관된 디자인과 아키텍처를 유지하도록 참조 정보 제공

---

## 디자인 토큰

| 토큰 | 값 | 용도 |
|------|------|------|
| `--color-bg` | `#0D0D1A` | 메인 배경 (딥 다크) |
| `--color-surface` | `#1A1A2E` | 카드/시트 배경 |
| `--color-primary` | `#7C3AED` | 강조 색상 (보라) |
| `--color-text` | `#F5F5FA` | 본문 텍스트 |
| `--color-text-dim` | `#6B7280` | 보조 텍스트 |
| `--color-joy` | `#FFD700` | 기쁨 감정 |
| `--color-sadness` | `#4A90D9` | 슬픔 감정 |
| `--color-anger` | `#E74C3C` | 분노 감정 |
| `--color-anxiety` | `#9B59B6` | 불안 감정 |
| `--color-peace` | `#2ECC71` | 평온 감정 |

## 해상도 기준

- **디자인 기준**: 440 × 956px (Figma artboard)
- **반응형 대응**: `MediaQuery`로 기기별 동적 스케일링
- **폴더블/태블릿**: 최대 600px 너비로 제한, 그 이상은 센터 정렬

## 주요 라이브러리

| 목적 | Flutter | Web (Capacitor) |
|------|---------|-----------------|
| 상태 관리 | Riverpod / Provider | Zustand / Jotai |
| 애니메이션 | ImplicitlyAnimatedWidget + CustomPainter | Framer Motion / CSS animations |
| 햅틱 | `HapticFeedback.lightImpact()` | Capacitor Haptics plugin |
| 로컬 저장 | shared_preferences + flutter_secure_storage | Capacitor Preferences |
| Firebase | firebase_core + cloud_firestore | Firebase JS SDK |
| 푸시 알림 | firebase_messaging | Capacitor Push Notifications |

## Firebase 데이터 모델

```yaml
collections:
  users:
    id: uid
    fields:
      - email: string
      - displayName: string
      - createdAt: timestamp
      - lastLoginAt: timestamp

  emotions:
    id: auto
    fields:
      - uid: string (FK → users)
      - emotion: string (joy/sadness/anger/anxiety/peace)
      - intensity: int (1-10)
      - note: string (optional)
      - createdAt: timestamp

  resolutions:
    id: auto
    fields:
      - uid: string (FK → users)
      - content: string
      - isCompleted: boolean
      - date: date
      - createdAt: timestamp
```

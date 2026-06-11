# 템플릿: 화면 전환 애니메이션 템플릿

> **파일 위치**: `prompts/templates/screen-transition-template.md`
> **용도**: 감정 상태/시간대별 화면 전환 로직 구현 시 재사용

---

## 구조

```yaml
transition:
  from: [이전 화면 ID]
  to: [다음 화면 ID]
  trigger: [수동_터치 | 자동_시간 | 감정_변화]
  duration: [전환 시간 ms]
  curve: [Curve 타입]

animation:
  type: [fade | slide | scale | wave | orb_morph]
  exit:
    opacity: [시작-끝]
    translate: [x, y]
    scale: [시작-끝]
  enter:
    opacity: [시작-끝]
    translate: [x, y]
    scale: [시작-끝]
  particle:
    enabled: [true/false]
    count: [입자 수]
    colors: [색상 배열]

haptics:
  enabled: [true/false]
  type: [light | medium | heavy | selection | success]

sound:
  enabled: [true/false]
  asset: [사운드 파일 경로]
```

## 사용 예시

```yaml
# 감정 기록 → AI 피드백 화면 전환
transition:
  from: emotion_input
  to: ai_feedback
  trigger: 수동_터치
  duration: 800
  curve: easeInOutCubic

animation:
  type: wave
  exit:
    opacity: [1.0, 0.0]
    translate: [0, -50]
  enter:
    opacity: [0.0, 1.0]
    translate: [0, 50]

haptics:
  enabled: true
  type: medium
```

## 통합 방법

1. 템플릿을 복사하여 `prompts/tasks/` 아래 새 태스크 파일 생성
2. `[ ]` 부분을 실제 화면 ID와 파라미터로 치환
3. Flutter: `PageTransitionSwitcher` 또는 `Hero` 위젯과 결합
4. Web: CSS `@keyframes` + React `framer-motion`과 결합

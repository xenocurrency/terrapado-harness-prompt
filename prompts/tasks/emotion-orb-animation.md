# 태스크: 감정 구체(Emotion Orb) 애니메이션 구현

> **파일 위치**: `prompts/tasks/emotion-orb-animation.md`
> **관련 시스템**: CustomPainter + AnimationController (Flutter) / Canvas API (Web)

---

## 목표

메인 화면 중앙에 위치한 2D 감정 구체(Orb)가 사용자의 현재 감정 상태에 따라 부드럽게 파동 치며 색상과 크기가 변화하는 애니메이션을 구현한다.

## 요구사항

1. **감정 매핑**
   - 기쁨(Joy) → 따뜻한 황금색(#FFD700), 부드럽게 확장하는 파동
   - 슬픔(Sadness) → 차분한 파란색(#4A90D9), 천천히 수축하는 파동
   - 분노(Anger) → 선명한 적색(#E74C3C), 빠르고 불규칙한 파동
   - 불안(Anxiety) → 연한 보라색(#9B59B6), 떨리는 듯한 미세 진동
   - 평온(Peace) → 에메랄드 그린(#2ECC71), 완전히 부드러운 호흡 파동

2. **인터랙션**
   - 사용자가 Orb를 터치/클릭하면 감정 선택 화면으로 전환
   - Orb는 주변에 미세한 입자(Particle) 효과를 방출
   - 감정이 선택되면 Orb가 해당 감정의 색상/패턴으로 600ms 전환 애니메이션

3. **성능**
   - 60fps 유지 (저사양 기기에서는 30fps로 자동 폴백)
   - CustomPainter의 `shouldRepaint` 최적화
   - 불필요한 위젯 리빌드 방지

## 출력 형식

- Flutter: `lib/widgets/emotion_orb.dart` + `lib/painters/orb_painter.dart`
- Web: `src/components/EmotionOrb.tsx` + Canvas API 구현체

## 평가 기준

- Orb가 부드럽게 움직이는가? (60fps)
- 감정 전환 애니메이션이 자연스러운가?
- 주사율이 다른 기기에서도 깨지지 않는가?

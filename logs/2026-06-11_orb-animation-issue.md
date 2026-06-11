# 실행 로그: Orb 애니메이션 프레임 드랍 이슈

> **파일 위치**: `logs/2026-06-11_orb-animation-issue.md`
> **세션**: 2026-06-11 오전

---

## 문제

Emotion Orb 애니메이션이 저사양 Android 기기(Galaxy A12)에서 30fps 이하로 드랍됨.

## 원인 분석

1. `CustomPainter`의 `paint()` 메서드 내에서 매 프레임마다 새로운 `Paint` 객체를 생성
2. Orb 파동 계산을 위해 매 프레임마다 복잡한 삼각함수 연산 수행
3. `setState()`가 불필요하게 상위 위젯 트리 전체를 리빌드

## 적용된 수정

1. `Paint` 객체를 `shouldRepaint()` 외부로 분리하여 캐싱
2. 삼각함수 연산 결과를 60프레임 버퍼에 프리컴퓨팅
3. `ValueNotifier`를 도입하여 Orb 위젯만 선택적 리빌드

## 결과

Galaxy A12 기준 55-60fps 유지 확인. 추가 최적화 불필요.

## 추후 관찰 사항

- Web(Capacitor) 환경에서는 Canvas 2D API로 대체 시 성능 유사함 확인
- 고주사율 디스플레이(120Hz)에서는 60fps 고정 필요 (불필요한 배터리 소모 방지)

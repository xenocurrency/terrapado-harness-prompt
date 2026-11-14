# Terrapado Harness Prompt — 개선안 v5.10

> **목적**: 기존 Flutter 앱을 안전하게 개선하는 Brownfield 개발용 운영 프롬프트
> **앱**: Terrapado — 감정 기록 및 자기 성찰 앱
> **기본 타깃**: Android / Flutter
> **프로젝트 루트**: `C:\coding\flutter_ui`는 기존 기록상 경로이며, 실행 시 실제 저장소와 현재 작업 경로를 확인한다.
> **최신 갱신**: 2026-11-14 (v5.10)
> **직전 문서**: `Terrapado_Harness_Prompt_v4.md` (이 문서 v5.x가 대체한다)

---

## 변경 이력 (Changelog)

### v5.10 — 2026-11-14

사용자가 승인한 리마인더 수정, UI 정리, 기록/통계 구조 개편 및 감정 기준선 보강을 반영했다. 아래 내용이 v5.9의 지층 시각화·나무 위치·히트맵 기간 명세를 대체한다.

1. **리마인더 미수신 수정**: Android `ScheduledNotificationReceiver`와 `ScheduledNotificationBootReceiver`, `RECEIVE_BOOT_COMPLETED` 등록. 기기에서 권한과 예약은 있으나 수신기가 없던 사실을 확인했다. 스마트 스킵은 이번 회차만 건너뛰고 다음 날 반복 예약을 유지한다(`reminder_schedule.dart`). 일일 채널 임시 예약 알림의 실제 발화 확인 후 검사 알림은 정리했다. 실제 재부팅 시험은 미수행.
2. **U1/U2/U4/U5/U7 적용**: `MainShell`에 홈/기록/통계 하단 탭. 기록 내부 감정/다짐 전환. 설정은 홈 우측 상단. 홈 고정 100px 여백 제거 및 지구구체 반응형 크기, 글자 위계·보조 설명 대비 개선, 설정/목록의 묶음 배경과 구분선, 주요 터치 영역 최소 48px 및 선택 체크 표시. U3 문구 전면 변경과 U6 통계 접기는 미적용.
3. **기록 탭**: My Strata 상단 감정 지층 시각화와 `emotionalLayers` 설명 제거. 감정 필터·확장 목록·페이지네이션·이미지 저장 유지. 다짐 탭에서도 나무를 제거하고 페이지네이션 목록만 유지하며, 기존 나무 전용 365건 조회는 제거했다.
4. **통계 히트맵/나무**: 감정 히트맵과 통계-다짐의 다짐의 나무에 각각 **90일/180일/1년(365일)** 선택. 기본 90일. 나무는 기록에서 이동하여 기존 다짐 패턴 카드를 대체한다. 날짜 범위 전체를 실제 조회하며 개수로 기간을 대체하지 않는다. `HistoryWindow`는 오늘 포함 달력일, `[시작일 00:00, 내일 00:00)`이다. 히트맵 500건 클라이언트 잘림을 해제하고, 나무는 `getResolutionsInDateRange`로 선택 범위를 조회한다. 기간별 캐시·중복 요청 방지·오래된 응답 덮어쓰기 방지·실패 재시도 지원. 최근 데이터가 비어도 긴 기간 선택 가능. 1년 히트맵은 가로 스크롤과 최소 14px 셀, 나무는 링 폭 압축으로 최신 기록까지 모두 표시.
5. **탐색 재선택**: 하단 기록 또는 통계가 이미 선택된 상태에서 같은 메뉴를 다시 누르면 해당 내부 감정↔다짐 탭 전환. 첫 진입 때만 화면을 만들고 이후 상태 유지, 숨은 탭 Ticker 중지, ValueListenable 신호 구독 해제 보장.
6. **대응·해소 비중 변화**: 누적 영역형 시리즈를 해소(노랑) 아래/대응(보라) 위로 배치. 데이터·색·범례 순서 동시 변경. 도넛은 기존 의미 유지.
7. **감정 기준선**: 최근 90일 **빈도 1위 감정과 비중** 유지. 모든 감정 종류(UNKNOWN 포함)의 기록 강도를 동등 가중하여 전체 평균과 **모표준편차(N으로 나눔)** 추가. Welford 방식으로 산출하고 화면은 소수 둘째 자리까지 표시한다. 기록 1건은 표준편차 0, 기록 없음은 빈 상태. 감정별 평균을 다시 평균 내지 않는다. 표준편차는 강도의 산포이며 시간순 전환 속도나 임상 진단을 뜻하지 않는다. 기준선 기간은 히트맵 선택기와 독립적인 90일이다. UI는 `EmotionBaselineCard`, 필드는 `meanIntensity`/`intensityStdDev`. 신규 문구 7개 언어 및 `flutter gen-l10n` 반영.
8. **검증**: `dart format` 완료. `flutter test test/emotion_baseline_test.dart test/history_range_test.dart test/main_navigation_test.dart test/reminder_schedule_test.dart` **18건 통과**. `flutter analyze`는 기존 2건만(§9.5). Galaxy Note 20 Ultra(Android 13)에 최신 `flutter run --dart-define-from-file=.env.json` 디버그 빌드 설치·실행, 통계-감정에서 기준 감정/평균/표준편차/설명 실제 표시 확인. 모든 기간 데이터와 재부팅의 실기기 전수 검증을 의미하지 않는다.

> 오늘의 지시 이력은 부록 K. 미승인 제안은 과거 이력만으로 추가 구현하지 않는다.

### v5.9 — 2026-11-13 (3차)

2026-11-13 세션(총 3차 구현)의 결과를 반영했다. v5.8 대비 **Nocturne 미스틱 마감 4건(N1/N3/N6/N7) + My Strata 지층 높이 고정(1차)**, **Night Glass iOS 감각 7건(B2/B3/B4/C1/C2/D2/D4) + 다짐의 나무 나이테 1년치(2차)**, **1차 묶음 컨트롤 교체 4건(A1/A2/A3/B1, 3차)**이 이루어졌다. 명령은 대화 직접 전달 방식이었다.

1. **Nocturne — 배경·CTA·차트·아이콘 마감 (N1/N3/N6/N7)** — §5
   - N1: `time_aware_theme.dart`에 **`AmbientBackground`/`_StarfieldPainter`** 신설 — 시간대 그라디언트 위 고정 시드(`Random(2026)`) 별밭(정적, paint 1회 캐시, 무한 애니메이션 없음 — §5 배터리 원칙 준수) + 하단 vignette. 홈 화면에 적용.
   - N3: 홈 CTA를 **오늘 첫 기록의 감정색 그라디언트 + 헤어라인 보더 + 감정색 외곽 글로우(blur 28, 정적)** 오브 버튼으로 재작. 기록 없으면 기존 중립 slate.
   - N6: 통계 차트 마감 — 도넛 슬라이스 헤일로·갭 확대(작은 슬라이스 비율 보존), 히트맵 기록 셀 상단 하이라이트, 누적 영역형 밴드 하단 페이드. `Gradient.linear` 미노출로 `LinearGradient().createShader()` 교체(3곳).
   - N7: 백버튼 톤 통일 — stats(컨테이너화)·My Strata·다짐의 나무(표준: white@0.08·r12·`arrow_back_ios_new` 18px).
   - **My Strata 지층 높이 고정**: `_strataHeight` — 첫 페이지(30건 → 80px) 로드 시 1회 산정 후 고정, 이후 페이지가 쌓여도 화면 비중 불변. 기록이 고정 높이를 넘으면 **레이어 압축**(`layerH = size.height/emotions.length`)으로 전체 기록 계속 표시.
2. **Night Glass — iOS 감각 7건 (B2/B3/B4/C1/C2/D2/D4)** — §5
   - **신규 공용 위젯 3종**: `lib/widgets/glass_sheet.dart`(`showGlassSheet` — 상단 r24 + BackdropFilter blur 18 + tint `#1E1E28@0.78`), `lib/widgets/stagger_in.dart`(`StaggerIn`/`staggered` — index*40ms, 최대 480ms, 1회성), `lib/widgets/frosted_header.dart`(`FrostedHeaderScaffold` — 헤더 높이 첫 프레임 실측 후 bodyBuilder 두 번째 인자로 전달).
   - B2: `main.dart` 전역 `pageTransitionsTheme` — android/iOS 모두 `CupertinoPageTransitionsBuilder`. **Flutter 3.44부터 이 클래스가 material이 아닌 cupertino 라이브러리 소속** — `show` 절 import 필요(SDK 소스 확인). grid→flow 라우트도 슬라이드+패럴랙스 교체.
   - B4: 홈 메뉴 시트 열림 시 본문 `AnimatedScale` 0.94(iOS 모달 감각) + 시트 내 탄성 스크롤.
   - C1: stats(헤더+TabBar)·My Strata 헤더를 **frosted 바**로 — 콘텐츠가 헤더 아래로 스크롤되며 흐릿하게 비친다. My Strata는 `CustomScrollView(slivers)` 리팩터(페이지네이션 `_scrollController` 유지).
   - C2: 홈 메뉴·감정 설명 bottom sheet → 유리 시트.
   - B3: stats 두 탭 섹션·My Strata 타일 순차 등장.
   - D2: 햅틱 정리 — 토글 전환·탭 전환(`indexIsChanging`)·시트 열기.
   - D4: `BouncingScrollPhysics` 통일 — stats×2·My Strata·다짐의 나무·홈 시트·감정 그리드.
   - **다짐의 나무 나이테 1년치(사용자 명시 요청)**: `_treeResolutions` 신설 — 목록 페이지네이션(30건)과 무관하게 초기 로드에서 `limit: 365` 별도 쿼리 1회로 확보, **스크롤과 무관하게 처음부터 완성형 나이테**. 링 폭 압축 `min(1.0, (maxRadius-5)/n)`. **Firestore 읽기 1회 추가가 사용자 승인 범위에 포함됨**.
3. **1차 묶음 — 컨트롤 교체 4건 (A1/A2/A3/B1)** — §5, §8.1
   - "1차 묶음" = Night Glass 제안(12건)의 우선순위 1세트 **A1+A2+A3+B1**을 가리키는 세션 용어다(사용자 이해를 위해 항목별 설명 제공 후 승인).
   - A1: 리마인더·주간요약 토글 2곳 → **`CupertinoSwitch`**(`activeTrackColor` — deprecated `activeColor` 회피, analyze 신규 이슈 방지).
   - A2: 리마인더 시각 편집 → **iOS식 휠 피커**(유리 시트 안 시/분 `CupertinoPicker` 2개 + 확인/취소) — 기존 `TimeOfDay` 파이프라인(저장·재예약 로직) 유지.
   - A3: 통계 탭바 → **iOS식 pill 세그먼트 컨트롤**(`_SegmentControl` — 유리 트랙 + 슬라이딩 썸, `_tabController` 그대로 구동해 TabBarView·스와이프 추적 유지). frosted 헤더 내 배치 유지.
   - B1: **`lib/widgets/pressable_card.dart` 신설** — 프레스 0.97 축소 + `easeOutBack` 스프링 복귀. **피드백은 `Listener`로 감지(제스처 아레나 비참여)** — 자체 GestureDetector로 탭을 처리하는 카드를 감싸도 탭이 깨지지 않는다. 적용: 홈 메뉴 4행·My Strata 타일·통계 기간 칩/강도 펼침 행.
   - **원상복구 장치(사용자 요청)**: 백업 폴더 `.backup_1cha_20261113/`(home/stats/today_log — 1차 묶음 직전 상태, **이전 라운드 변경분 포함 주의**) + 코드 마커 주석(`// A1(1차 묶음):` 등). 점 폴더라 analyze 대상에서 제외됨.
4. **PROPOSE_ONLY 미적용 유지**: 디자인 토큰화 D1–D8, Nocturne 잔여(N2/N4/N5/N8), Night Glass 잔여(C3/D1/D3), 경량화 P1–P5/R2–R4/S1–S2. 전부 승인 전 미적용.
5. **기존 이슈 불변 확인**: `flutter analyze` 기존 3건(§9.5) 외 신규 이슈 0, `dart format` 완료. 실기기 검증은 전 항목 미수행 — frosted/유리(`BackdropFilter`) 저사양 프레임 소실 가능성은 `flutter run`(§9.4)으로 확인 필요.

> v5.8의 나머지 원칙(throttle 통일·기간 선택기·히트맵 탭 상세·리마인더 시각 편집·주간 요약·홈 위젯)은 유지한다. 2026-11-13 명령 이력은 **부록 J**에 있다.

### v5.8 — 2026-11-12 (2차)

2026-11-12 2차 명령(UI 개선 4건 + 경량화 R1)의 결과를 반영했다. v5.7 대비 **완료 화면 대기 단축(A2)**, **기간 선택기 헤더 배치(B2)**, **히트맵 오늘 셀 외곽선(B3)**, **My Strata 감정 타입 필터(D1)**, **지구구체 생명주기 제어(R1)**가 이루어졌다. 명령은 대화 직접 전달 방식이었다.

1. **완료 화면 대기 단축 (A2)** — §7.1
   - Emotion Flow 완료 화면의 진입 게이트 대기를 **AI가 initState 시점에 이미 준비돼 있으면 2초 → 800ms(`_readyDelayFast`)로 단축**한다. 미준비 시 기존 2초 유지.
2. **기간 선택기 헤더 배치 (B2)** — §5.1
   - v5.7의 기간 선택기를 별도 행에서 **"감정 분포" 섹션 헤더 우측(`_SectionHeader.trailing`)으로 이동**했다. 카드 간 낭비 공간 제거.
3. **히트맵 오늘 셀 외곽선 (B3)** — §5.1
   - `_HeatmapCellPainter`에서 **오늘 셀에 흰 외곽선(alpha 0.65, 1.2px)**을 그려 오늘 위치를 즉시 식별 가능하게 했다.
4. **My Strata 감정 타입 필터 (D1)** — §5 My Strata
   - 목록 위 **필터 칩(전체 + 7종, emoji·타입 색)** 신설. Firestore 쿼리를 바꾸지 않는 **클라이언트 필터** — 복합 인덱스 불필요. 필터 결과가 로딩분보다 적으면 다음 페이지를 자동 이어 받아(`hasMore` 소진 시 정지) 목록을 채운다. 확장 상태는 인덱스가 아닌 **기록 id 기준**으로 유지. 지층 시각화는 무필터 유지.
   - l10n: `strataFilterAll`·`strataNoRecordsType` 신설(7개 언어).
5. **지구구체 애니메이션 생명주기 제어 (R1, 경량화)** — §5
   - `_EmotionGlobeState`에 `WidgetsBindingObserver` 추가: **앱 백그라운드/화면 꺼짐(`paused`/`inactive`/`detached`)에서 `repeat()` 정지**, `resumed` 복귀 시 현재 각도에서 재개. 라우트에 가려짐은 프레임워크 `TickerMode`가 이미 처리(변경 없음). 무한 회전 2컨트롤러(30s 지구구체/3s 감정구체)의 배터리 소모 절감.
6. **경량화 제안(P1–P5/R2–R4/S1–S2)은 PROPOSE_ONLY 유지** — 부록 I. 미적용.
7. **기존 이슈 불변 확인**: `flutter analyze` 기존 3건(§9.5) 외 신규 이슈 0, `dart format` 완료. 실기기 검증은 전 항목 미수행.

> v5.7의 나머지 원칙(throttle 통일·기간 선택기·히트맵 탭 상세·리마인더 시각 편집·주간 요약·홈 위젯)은 유지한다. 2026-11-12 명령 이력은 **부록 I**에 있다.

### v5.7 — 2026-11-12 (1차)

2026-11-12 1차 명령(사용성 개선 6건 A1/B1/B2/C1/C2/D2)의 결과를 반영했다. v5.6 대비 **AI throttle 통일 + 완료 화면 3문장 스태거(A1)**, **통계 기간 선택기(B1)**, **히트맵 셀 탭 상세(B2)**, **리마인더 시각 개별 편집(C1)**, **주간 요약 알림(C2)**, **홈 위젯(D2)**이 추가되었다. 명령은 대화 직접 전달 방식이었다.

1. **AI throttle 처리 통일 + 완료 화면 스태거 (A1)** — §7.2, §7.6
   - Emotion Flow 경로의 throttle이 3초 창에서 영어 fallback 즉시 반환 대신 **남은 시간만큼 대기 후 호출**로 변경 — 통계 인사이트 경로(v5.3)와 동일 방식으로 통일. §7.6 영어 출력 후보 1 적용.
   - 완료 화면 AI 3문장을 동시 노출 대신 **스태거 등장(0/800/1600ms)** — `TypingText`에 `startDelayMs` 파라미터 신설.
2. **통계 기간 선택기 (B1)** — §5.1
   - 7/30/90일 선택기 신설(기본 90일). 적용 대상: 감정 분포 도넛·누적 영역형·최대 강도 Top 3·평균 강도·전환 경향·멘션 패턴. **히트맵·기준선은 90일 고정**. 영역형 구간 수 = 7일이면 7, 아니면 13. `statsPeriod7` 신설.
3. **히트맵 셀 탭 → 일자 상세 (B2)** — §5.1
   - 히트맵 셀 탭 시 해당 일자의 기록 목록(감정 배지·시각·강도·멘션)을 **bottom sheet**로 표시. 셀 좌표 판별(`_hitTestCell`)은 페인터 기하와 동일 규칙. 기록 없는 셀 탭은 `statsNoRecordsDay` 안내.
4. **리마인더 시각 개별 편집 (C1)** — §8.1
   - 홈 메뉴 리마인더 타일에 3개 시각 칩 표시, **탭해 시간 선택기로 개별 변경**(SharedPreferences `reminder_time_$i`, "HH:mm", 기본 08:00/12:00/19:00). 저장 즉시 재예약(스마트 스킵·exact 폴백 등 기존 규칙 유지).
5. **주간 요약 알림 신설 (C2)** — §8.2
   - 매주 일요일 19:00, 최근 7일 최빈 감정 기반 요약. 채널 `terrapado_weekly`, id 10, 토글 기본 켜짐(홈 메뉴 별도 타일).
6. **홈 위젯 신설 (D2, Android)** — §8.3
   - 네이티브 AppWidget(4×1)으로 최근 감정 표시 + 탭 시 감정 선택 화면 진입. `home_widget` 패키지 미사용 — MethodChannel `com.terrapado/widget`으로 Dart→Kotlin 푸시. 신규 파일: `lib/services/widget_service.dart`, `TerrapadoWidgetProvider.kt`, `WidgetHelper.kt`, 위젯 layout/drawable/xml/strings, Manifest receiver.
7. **ARB**: 신규 키 5종(`statsPeriod7`, `statsNoRecordsDay`, `reminderTimesEditable`, `weeklySummary`, `weeklySummaryCaption`) 7개 언어 + `flutter gen-l10n`. en `weeklySummaryCaption`은 작은따옴표 이스케이프(`week''s`) 필요.
8. **기존 이슈 불변 확인**: `flutter analyze` — 구현 중 `EmotionType` import 누락 에러 2건 발생 → 수정 후 기존 3건(§9.5)만, `dart format` 완료. 실기기 검증은 전 항목 미수행(릴리즈 빌드 + `adb install`은 사용자 담당).

> v5.6의 나머지 원칙(기준선 빈도 정의·연속 곡선·라벨)은 유지한다. 2026-11-12 명령 이력은 **부록 I**에 있다.

### v5.6 — 2026-11-07 (2차)

2026-11-07 2차 명령(기준선 재정의 + 그래프 후속 수정 + 라벨 변경)의 결과를 반영했다. v5.5 대비 **감정 기준선 정의 변경(빈도 우세 감정)**, **강도 변화 그래프 연속 곡선화**, **"생존 위협" → "외부환경 대응" 라벨 3키 전환**이 이루어졌다. 명령은 `C:\coding\opencode prompt_right now action.txt` 파일 전달 방식이었다.

1. **감정 기준선 재정의 (v5.5 정의 대체)** — §5.1
   - 감정 기준선 = **감정들 중 어떤 감정이 가장 우세한지, 빈도로 판정**한다. 레코드 수 1위 감정 타입 + 그 비중(`StatsService.emotionBaselineStats()` 재작성 — `dominant`/`share`/`recentDominant`/`recentShare`).
   - v5.5의 강도 가중 valence 0–100 지수 방식은 폐기. 카드도 동일하게 교체: 우세 감정명(감정 색) + `(비중)%` + 기록 존재 날 수 + **최근 14일 우세 감정 비교행** + 캡션. valence 지수·게이지 마커·기준선 대비 증감(trending) 표기는 제거.
   - 동률 판정은 `emotionDistribution`의 정렬 순서(레코드 수 내림차순)를 따른다. AI digest의 baseline 행도 `Emotion baseline: {영문 감정명} dominant (X% of N records)` + `Last 14 days: ...` 형식으로 교체했다(결정적 토큰만, 원문 없음).
   - **"어떻게 하는 게 행복한지" 분석은 여전히 사용자 후속 명령 대기**다.
2. **강도 변화 그래프 연속 곡선화** — §5.1
   - 평균 강도 행 펼침 그래프에서 **점 표기 제거** — 기록 사이를 2차 베지어(중점 스무딩)로 잇는 연속 곡선만 표시한다. 기록 1건은 짧은 수평 세그먼트로 위치만 표시. 눈금(1/5/10)은 유지.
3. **라벨 변경: "생존 위협" → "외부환경 대응"** — §5.1
   - '생존 위협과 관계' → **'외부환경 대응과 관계'**(`statsThreatRelation`), 그룹 라벨 '생존 위협' → **'외부환경 대응'**(`statsThreat`), 영역형 제목 → '외부환경 대응·관계 비중 변화'(`statsThreatRelationTrend`).
   - **의미상 그룹 멤버는 변경 없음**: 외부환경 대응 = 슬픔/분노/두려움/기쁨(`threatGroup` 동일). 대응(슬픔/분노/두려움)·해소(기쁨) 도넛 ②는 기존 표기 유지(변경 대상 아님).
   - 키 이름(`statsThreat*`)은 코드 참조 안정성을 위해 유지하고 값만 교체했다. 7개 언어 + `flutter gen-l10n` 재생성.
4. **기존 이슈 불변 확인**: `flutter analyze` 기존 3건(§9.5) 외 신규 이슈 0, `dart format` 완료. 실기기 검증은 전 항목 미수행.

> v5.5의 나머지 원칙(누적 영역형 3종·도넛 아래 배치·캐리포워드)은 유지한다. 2026-11-07 2차 명령 이력은 **부록 H**에 있다.

### v5.5 — 2026-11-07

2026-11-07 세션(감정 기준선 + 누적 영역형 + 강도 변화 그래프)의 결과를 반영했다. v5.4 대비 **감정 기준선 카드 신설**, **누적 영역형 그래프 3종(도넛 아래 배치)**, **평균 강도 행 탭 → 강도 변화 그래프**가 추가되었다. 명령은 `C:\coding\opencode prompt_right now action.txt` 파일 전달 방식이었다.

1. **감정 기준선 (baseline mood) 신설** — §5.1, 감정 탭
   - 통계 감정 탭 최상단(AI 해석 카드 다음, 히트맵 앞)에 "감정 기준선" 카드를 신설했다. 지수 산식: **강도 가중 valence** — 각 감정의 `circumplexX`(0–1)를 기록 강도로 가중 평균한 뒤 0–100 지수로 환산한다(`StatsService.emotionBaselineStats()` 순수 함수).
   - 카드 구성: 전체 90일 기준선 지수(큰 수 `/ 100`) + 기록 존재 날 수 + 게이지 마커 2개(흰색 = 기준선 90일, 보라 `#A29BFE` = 최근 14일) + 범례 행(기준선/최근 14일 지수) + **기준선 대비 증감**(±, trending 아이콘) + 캡션(`statsBaselineCaption`).
   - **목적의 범위**: 이 지표는 "꾸준히 기록한 감정 데이터로 감정 기준선을 아는 것"이 **첫 단계**다. "감정 기준선을 어떻게 하면 행복한지"에 대한 분석·개선은 사용자가 이후 별도 명령으로 확장할 예정이라 현재 구현은 기준선 표시까지다.
   - AI 인사이트 digest(`buildEmotionDigest`)에도 결정적 1행 `Emotion baseline: X/100, recent 14 days: Y/100`을 추가했다(원문·토큰 없음, §7.5 유지).
2. **누적 영역형 그래프 3종 신설** — §5.1, 감정 탭
   - 감정 분포 / 대응·해소 / 위협·관계 도넛 **각각의 바로 아래**에 `_StackedAreaChart` 카드를 배치했다: 현재 분포(도넛) → 시간 흐름 따른 비중 변화(영역형) 순서로 읽는 구성.
   - 산출: `StatsService.seriesShareOverTime(records, series, days: 90, buckets: 13)` — 90일을 주 단위 13구간으로 나눠 시리즈별 비중(합 = 1)을 산출하는 순수 함수 (`SeriesShareSlice`). **기록 없는 구간은 직전 비중 캐리포워드**로 곡선 단절을 막는다. unknown은 어느 시리즈에도 속하지 않으면 분모 제외(그룹 도넛과 동일 규칙).
   - UI: `_StackedAreaPainter` — 0/50/100% 눈금, 하단 날짜 라벨(첫·끝 + 4구간마다 `M/D`), 2차 베지어 스무딩 누적 밴드 + 상단 경계선, 범례 Wrap. 카드 제목: `statsDistributionTrend`/`statsDefenseReliefTrend`/`statsThreatRelationTrend` 신설.
3. **평균 강도 행 탭 → 강도 변화 그래프** — §5.1, 감정 탭
   - "감정별 평균 강도" 행을 `_IntensityExpandableRow`(StatefulWidget)로 교체. **행을 탭하면 펼쳐지며** 해당 감정의 시간 흐름별 강도 변화 그래프(`_IntensityTrendChart`/`_IntensityLinePainter`)를 표시한다. 토글 상태는 expand_more/less 아이콘으로 알린다.
   - 그래프: 기록 1건 = 점 1개, 세로 1–10 눈금(1/5/10), x는 첫~마지막 기록의 실제 날짜 비율 배치(1건은 중앙), 점 연결선(감정 색). 카드 내부 헤더에 `statsIntensityTrend` + `statsPeriod90` 배지.
   - 기존 `_IntensityRow` 클래스는 삭제(표기 중복·미사용 경고 방지).
4. **ARB**: 신규 키 8종(`statsBaseline`, `statsBaselineCaption`, `statsPeriod14`, `statsBaselineCompare`, `statsDistributionTrend`, `statsDefenseReliefTrend`, `statsThreatRelationTrend`, `statsIntensityTrend`) 7개 언어 추가 + `flutter gen-l10n` 재생성. fr은 ICU 단일 인용부호 이스케이프(`l''intensité`) 필요.
5. **기존 이슈 불변 확인**: `flutter analyze` 기존 3건(§9.5) 외 신규 이슈 0, `dart format` 완료. 실기기 검증은 전 항목 미수행.

> v5.4의 나머지 원칙은 유지한다. 2026-11-07 세션의 시간순 명령 이력은 **부록 G**에 있다.

### v5.4 — 2026-11-06

2026-11-06 세션(통계 보강 + 알림 개선)의 결과를 반영했다. v5.3 대비 **그룹 감정분포 원형 그래프 2종**, **Top 3 peak 일자·멘션 표기**, **다짐 나이테 패턴 섹션**, **리마인더 스마트 스킵 축소·exact alarm 전환**이 추가되었다. 명령은 `C:\coding\opencode prompt_right now action.txt` 파일 전달 방식이었다.

1. **감정분포 그룹 원형 그래프 2종 신설** — §5, 부록 F
   - 기존 감정 분포 도넛 하단에 그룹 도넛 카드 2종을 추가한다: **생존 위협 대응(슬픔/분노/두려움) vs 해소(기쁨)**, **생존 위협(슬픔/분노/두려움/기쁨) vs 관계(사랑/싫음)**.
   - 산출: `StatsService.emotionGroupStat( records, group )` + `defenseReliefStats()`/`threatRelationStats()` 순수 함수 + `EmotionGroupStat` 데이터 클래스. 그룹 구성은 서비스 상수(`responseGroup`/`reliefGroup`/`threatGroup`/`relationGroup`)로 관리한다.
   - **unknown은 어느 그룹에도 속하지 않는다** — 그룹 도넛에서 제외, 개별 감정 도넛에는 계속 포함.
   - UI: `_GroupDonutChart`(2슬라이스 도넛 + 그룹 비율 범례 + 멤버별 기록 수), `_SegmentsDonutPainter`. 그룹 슬라이스 색: 대응 보라 `#6C5CE7` / 해소 `#FFD93D` / 위협 `#FF4757` / 관계 `#FF6B9D`.
2. **가장 강했던 감정 Top 3 — 언제·멘션 표기** — §5
   - `EmotionIntensityStat`에 `peakDate`(최대 강도 기록 중 가장 최근 1건)·`peakNote` 신설. Top 3 행에 일자(`M/D`)와 멘션 원문(최대 2줄, 있을 때만)을 표시한다(`_IntensityPeakRow`). 평균 강도 섹션은 기존 `_IntensityRow` 유지.
3. **다짐 패턴 — 다짐의 나무 나이테 참조** — §5
   - 다짐 탭에 "다짐 패턴" 섹션 신설(추이 바 차트와 키워드 사이, 배지 = 최근 30일): 다짐의 나무 화면(resolution_history_screen)과 동일한 나이테 시각화를 미니 재현 — 다짐 1건 = 1링(가운데=오래된 것), 링 색 = 성취도(복기 점수/완료 여부), 범례 완료/미완료 + `treeWillGrow` 캐프션.
   - 나이테 색 헬퍼를 `lib/utils/resolution_achievement_color.dart`(`resolutionAchievementColor()`)로 공용 추출 — 다짐의 나무 화면도 해당 util 사용(드리프트 방지).
4. **알림 리마인더 개선 (문서 v5.3 항목의 후속 수정)** — §8.1
   - **스마트 스킵 창 축소: 3시간 → 45분**. 기존 3시간 창은 아침 9:15 기록이 12:00 팝업을 통째로 소멸시키는 과잉 억제였고, "팝업이 안 뜬다" 보고의 1차 원인으로 동작 확인됨. 이후 직전 45분 이내 기록일 때만 그 회차를 자제한다.
   - **상시 알림 보존(v5.3 회귀 수정)**: `scheduleAllReminders()`의 `cancelAll()` → `_cancelReminders()`(id 0..2 전용) 교체. 감정/다짐 저장 후 예약 갱신 시 상시(persistent, id 9999) 알림이 지워지지 않는다.
   - **exact alarm 전환**: `AndroidScheduleMode.exactAllowWhileIdle` 사용 전 `canScheduleExactNotifications()` 확인, 불가 시 `inexactAllowWhileIdle` 폴백. 스케줄 로그에 `exact: true/false` 포함. 매니페스트에 `SCHEDULE_EXACT_ALARM` 권한 선언(Android 12+).
   - 알림 원인 조사에서 **timezone 미설정(`tz.setLocalLocation` 부재)은 발화 시각 오차 원인이 아님** 확인 — `TZDateTime.from`은 절대 시점(epoch)을 보존(timezone 0.10.1 소스 확인). DST 지역 재사용 시 잠재 리스크로만 기록.
5. **기존 이슈 불변 확인**: `flutter analyze` 기존 3건(§9.5) 외 신규 이슈 0. 실기기 검증은 전 항목 미수행.

> v5.3의 나머지 원칙은 유지한다. 2026-11-06 세션의 시간순 명령 이력은 **부록 F**에 있다.

### v5.3 — 2026-11-05

2026-11-05 세션(총 3차 구현)의 결과를 반영했다. v5.2 대비 **통계 감정 탭 보강·다짐 복기 추이 제거**, **통계 AI 해석 카드 신설(감정/다짐)**, **알림 리마인더 개편(08/12/19·토글)**이 추가되었다.

1. **통계 감정 탭 보강** — §5, 부록 E
   - **도넛 범례 개수 컷 제거**: 기존 Top 6 제한(`take(6)`)을 없애 기록된 감정은 모두 범례에 표시한다(7종 전체 기준).
   - **"가장 강했던 감정 — Top 3" 신설**: 감정별 최대 강도 내림차순, 동률은 평균 강도 순. 행 스타일은 전환 경향 행과 동일(감정 배지 + `9/10`). **(v5.4 갱신: 각 행에 peak 일자 `M/D`와 당시 멘션 원문(최대 2줄)을 함께 표시 — `_IntensityPeakRow`)**
   - **"감정별 평균 강도" 신설**: 기록이 있는 전체 감정 대상, 평균값 내림차순, `7.3/10` 표기.
   - 신규 산출 코드: `lib/services/stats_service.dart`의 `emotionIntensityStats()` + `EmotionIntensityStat` 데이터 클래스. 섹션 순서는 히트맵→도넛→**그룹 도넛 2종(v5.4)**→최대 강도 Top 3(peak 일자·멘션 표기, v5.4)→평균 강도→전환 경향→멘션 패턴.
   - l10n: `statsIntensityTop`·`statsAvgIntensity` 신설(7개 언어).
2. **다짐 탭 복기 추이 제거** — 부록 E
   - 저녁 복기 라인 차트 섹션과 전용 위젯 2개(`_ReflectionLineChart`/`_ReflectionLinePainter`)를 제거했다. 지표 카드의 "평균 복기"는 유지한다.
   - l10n: `statsReflectionTrend`·`statsNoReflection` 키를 7개 언어에서 삭제했다. 다짐 탭 구성: 지표 4종 → 다짐 추이 바 차트 → 다짐 키워드 Top 3.
3. **통계 AI 해석 카드 신설** — §7.2, 부록 E
   - 감정/다짐 탭 **최상단에 "AI 해석" 카드**를 둔다. 사용자 명시 요청으로 v5.2의 "통계는 클라이언트 결정적 집계 단일 경로, AI 호출 없음" 원칙을 **대체**했다(§0 지시 우선순위 1 적용).
   - 구현: `AiService.generateStatsInsight({kind, summary, languageCode})` — `kind`는 'emotion'|'intention'. 요약 근거는 `StatsService.buildEmotionDigest()/buildIntentionDigest()` 순수 함수가 만드는 결정적 영문 digest로, **집계 수치·키워드 토큰만** 포함하고 사용자의 원문 문장·다짐 내용 전문은 포함하지 않는다(§7.5 기본 규칙 유지, 토큰 인용만 허용).
   - **캐시 계약**: 캐시 키 = `종류|언어|오늘 날짜|summary 해시`. 같은 날 + 같은 데이터면 반복 진입 시 재호출 없이 회수한다. 데이터가 바뀌거나 날이 바뀌면 재생성. 동일 키 진행 중 요청 dedup.
   - **throttle 차이(§7.6과 다름)**: 통계 인사이트 경로는 throttle 3초 창에 걸리면 fallback을 즉시 반환하지 않고 **남은 시간만큼 대기 후 호출**한다(Emotion Flow 경로는 §7.6 그대로 — 미수정).
   - UI: 기존 `TypingText` 위젯 재사용(워드 페이드 애니메이션), 도착 전 로딩 자리 유지, 생성 실패 시 카드 자체를 축소한다. 실패 로그에 digest·원문을 남기지 않는다.
4. **알림 리마인더 개편** — §8
   - 시각 변경: (08,14,21) → **08:00 / 12:00 / 19:00** — "감정입력 권장" 알림.
   - **토글 신설**: SharedPreferences `reminder_enabled`, **기본값 켜짐**. 홈 '三' 메뉴의 기존 권한 요청 타일을 **스위치 토글 타일로 개편**(하단에 `08:00 · 12:00 · 19:00` 표기). OFF 시 예약된 리마인더만 취소(id 0..2) — FCM·상시(persistent) 알림은 유지.
   - `scheduleAllReminders()` 진입부에서 토글을 확인한다 — 기존 호출부(main 스플래시, auth 로그인, 감정 저장 후, 다짐 저장 후)는 코드 변경 없이 게이트를 자동 통과한다.
   - 기존 스마트 스킵 유지: 알림 시각 **직전 45분** 창 내 마지막 감정 기록이 있으면 해당 회차 스킵(v5.4 축소). OS 알림 권한이 없는 상태로 ON하면 권한을 재요청하고 거부 시 `notificationDeniedHint` 스낵바를 표시한다.
5. **기존 이슈 불변 확인**: `flutter analyze` 기존 3건(§9.5) 외 신규 이슈 0.

> v5.2의 나머지 원칙(운영 원칙, ID 체계, Firebase/개인정보, 실행·보고)은 유지한다. 2026-11-05 세션의 시간순 명령 이력은 **부록 E**에 있다.

### v5.2 — 2026-11-03

2026-11-03 세션의 구현 결과를 반영했다. v5.1 대비 **통계 화면 신설(SVC01_STATS / P30_STATS)**과 **UNKNOWN 감정 멘션 AI 해석**이 추가되었다.

1. **통계 화면 신설** — §5, §6.2, 부록 D
   - 홈 '三' 메뉴에 통계 진입 타일 신설 → `P30_STATS`(감정/다짐 2탭, 감정 기본).
   - **감정 탭**: 최근 90일 감정 히트맵(주 단위 그리드, 일자별 지배 감정 색+밀도 알파, 월 라벨·범례) / 감정 분포 도넛 차트(Top 6 범례) / 감정 전환 경향 Top 3(동일 감정 연속 제외) / 멘션 패턴 Top 3(멘션 키워드→당시 감정, 최빈 감정 과반+2회 이상만 채택).
   - **다짐 탭**: 지표 카드 4종(다짐 수/완료율/연속일/평균 복기) + 30일 다짐 추이 바 차트(높이=일별 다짐 수, 색=완료 여부) + 저녁 복기 라인 차트(0–10, 복기 기록 날만 점정) + 다짐 키워드 Top 3(기록당 토큰 dedup, 조사 제거 근사 파서). **※ 복기 라인 차트는 v5.3에서 제거됨** — 현재 구성은 v5.3 변경 이력 참조.
   - **신규 파일**: `lib/services/stats_service.dart`(SVC01_STATS — 로딩은 기존 FirestoreService 재사용, 산출은 전부 클라이언트 순수 함수·결정적, AI 호출 없음), `lib/screens/stats_screen.dart`.
   - **데이터/비용 원칙**: 신규 컬렉션·쿼리 없음(기존 `getEmotionsInDateRange` 1회 + resolution 히스토리 1페이지). Firestore 복합 인덱스 신규 불필요.
   - **멘션/다짐 파서 근사성 문서화**: 형태소 분석이 아닌 trailing 조사 제거 근사 + 불용어 필터. 키워드 채택은 "최빈 감정 과반" 조건으로 오판을 억제한다. 형태소 분석기 도입 등의 개선은 PROPOSE_ONLY.
   - l10n: 통계 관련 키 20여 개 신설(`statistics`, `stats*` 계열) 7개 언어 전부, `flutter gen-l10n` 재생성. 다짐 키워드 섹션의 기간 배지는 로딩 범위(30일)와 불일치해 제거했다.
2. **UNKNOWN 감정 멘션 AI 해석** — §7.2, §7.4(계약 변경)
   - 사용자 요청: '알수없음' 선택 + 멘션 입력 시, 5개 원형(희=JOY/노=ANGER/애=SORROW/구=FEAR/오=DISLIKE) 중 가까운 감정을 AI가 판정해 stage 3 피드백에 자연스럽게 녹임(라벨·원문 인용 금지).
   - 구현: `AiService.generateAllTexts`에 `String? mention` 파라미터 신설(감정 입력 stage 1 Confirm/Skip의 `_mention`, stage 2는 `initialMention` 경유). **멘션 원문 프롬프트 금지 기본 규칙(§7.5)의 명시적 예외** — UNKNOWN + 멘션 있는 경우에만 분류 근거로 포함, 그 외 경로는 기존 원칙 유지.
   - UNKNOWN이지만 멘션이 없는 경우는 기존 특수 지시(v4 유지)를 그대로 사용.
3. **기존 이슈 불변 확인**: `flutter analyze` 기존 경고 3건(§9.5)만 존재, 신규 이슈 0.

> v5.1의 나머지 원칙(운영 원칙, ID 체계, Firebase/개인정보, 알림·딥링크, 실행·보고)은 유지한다. 2026-11-03 세션의 시간순 명령 이력은 **부록 D**에 있다.

### v5.1 — 2026-10-24

2026-10-24 세션의 구현·조사 결과를 반영했다. v5.0 대비 감정입력 UX 3건과 Apple 로그인 UI 회수가 추가되었다.

1. **감정 이름 로케일 표시** — §5, `lib/utils/emotion_name_l10n.dart`
   - 감정 카드·`?` 설명 시트·감정입력 헤더의 감정 이름이 앱 로케일로 표시된다(JOY→기쁨). ARB의 `emotionJoy`~`emotionUnknown` 기존 키를 활용(신규 ARB 불필요).
   - `EmotionType.localizedName(l10n)` 확장 헬퍼로 통일. Analytics 이벤트 표기는 영문 `displayName` 유지.
2. **감정입력 stage 1 키보드 대응** — §5
   - 멘션 입력 중 키보드 표시 시 `viewInsets.bottom > 8`을 감지해 오브 영역을 250ms로 접고 간격 40→8 축소. Skip/Confirm 버튼이 키보드 위 가시 영역에 유지된다. 완료 stage에는 영향 없음.
3. **다짐 설정 화면 입력 유도** — §5
   - `ResolutionScreen` 설정 모드 진입 시 필드 자동 `requestFocus()`로 키보드 선제시(복기 모드 제외).
4. **Apple 로그인 버튼 숨김(전 플랫폼)** — §9.5
   - 사용자 결정: 현재 단계에서는 미사용으로 모든 플랫폼에서 UI 버튼·핸들러 제거(auth_screen.dart).
   - **조사 기록**: Apple 로그인은 코드상 구현 완료 상태였음(`sign_in_with_apple` ^6.1.0, nonce+sha256, `OAuthProvider('apple.com')`, Apple 재인증, l10n 7개 언어). 그러나 `getAppleIDCredential`은 iOS/macOS 전용 — Android에서는 웹 리다이렉트 브리지(소유 HTTPS 도메인 + Apple Services ID 등록 + 서버) 없이 불가. **서비스/프로바이더 계층은 유지** — 재활성화 시 UI만 복원하면 됨. Firebase Console의 apple.com provider 활성 여부는 Console 확인 필요(리포 확인 불가).

> v5.0의 나머지 원칙(운영 원칙, ID 체계, Firebase/개인정보, 알림·딥링크, 실행·보고)은 유지한다. 2026-10-24 세션의 시간순 명령 이력은 **부록 C**에 있다.

### v5.0 — 2026-10-21

2026-10-21 세션의 결정과 구현·조사 결과를 반영했다. v4.0과 달라진 핵심은 **daily stats 컬렉션 폐기**와 **AI 피드백 이슈 조사 기록**이다.

1. **Firestore 컬렉션 감사 및 daily stats 폐기** — §6.2
   - 클라이언트 코드에서 참조하는 컬렉션은 총 9개였으나, `daily_emotion_stats`/`daily_user_stats`는 **이전 버전이 남긴 잔재**로 판정되었다.
   - **판정 근거**: `daily_emotion_stats`는 쓰기만 존재하고 읽기 consumer가 전혀 없었음(dead code). `daily_user_stats`는 읽기(consumer)가 있었으나 전부 `emotion_globe` 기반 UID dedup 산출로 대체 가능했다. Cloud Functions 폴더도 없어 서버 소비자 없음.
   - **처리**: 클라이언트 코드에서의 호출을 전부 제거했다(§6.2). DB 컬렉션 자체는 **사용자가 Firebase Console에서 수동 삭제**하기로 했다. 삭제 이후에도 코드가 write를 남기면 컬렉션이 다시 생성되므로, 이 문서는 v5 시점 코드에 참조가 없음을 기준으로 한다.
2. **고유 사용자 수 데이터 소스 단일화** — §6.2, §7.3
   - 오늘의 글로벌 고유 사용자 수는 **emotion_globe 공개 데이터의 UID dedup 클라이언트 산출**이 유일 경로가 되었다. 저장 직후의 첫 기록 반영은 인메모리 증분(`_dailyUserCount++`)으로 유지한다.
3. **AI 피드백 — 지연 + 영어 출력 이슈 조사 (INVESTIGATE 완료, 판별 미완)** — §7.6, 부록 B
   - 사용자 보고: 감정 입력 완료 후 AI feedback 표시가 느리고, 영어로 나온다.
   - languageCode 전달 파이프라인(`Localizations.localeOf` → `generateAllTexts(languageCode:)` → `OUTPUT LANGUAGE` 프롬프트 지시)은 코드상 **정상** 확인. 따라서 영어 출력은 **fallback 텍스트가 표시된 케이스**로 한정 추정했다.
   - 1차 로그(`C:\coding\error message.txt`)는 홈 지구구체만 캡처됨 + `[Globe]` 매 프레임 도배로 **AI 시점 미포함** → 판별 재수집 필요. 확증 절차는 §9.6에 추가했다.
4. **신규 기존 이슈: 지구구체 Painter 로그 도배** — §9.5
   - `emotion_globe_painter.dart`의 `debugPrint('[Globe] Drawing TEXTURE sphere (2048x1024)')`가 매 paint 프레임 기록되어 logcat을 도배한다. 저장 제안 상태(미적용)이지만, 어떤 로그 분석도 방해하므로 **로깅 완화(최초 1회 또는 샘플링)를 함께 제안**한다.
5. **firestore.rules 리포 미존재 확인**
   - `firestore*.rules` 파일이 리포에 없다(§1 점검). 보안 규칙이 Firebase Console에만 존재할 가능성. 코드 변경 시 규칙 정합성 확인이 필요하면 Console에서 확인한다.

> v4.0의 나머지 원칙(운영 원칙, ID 체계, Firebase/개인정보, 알림·딥링크, 실행·보고)은 유지한다. v5 시점의 코드·문서 불일치는 **현재 소스와 이 문서**를 기준으로 한다. 2026-10-21 세션의 시간순 명령 이력은 **부록 B**에 있다.

---

## 0. 최우선 운영 원칙

당신은 Terrapado의 **기술 리드 겸 구현 담당자**다. 목표는 사용자의 의도를 정확히 구현하는 것이며, 지시문을 그대로 코드에 기계적으로 옮기는 것이 아니다. 기존 소스 코드를 먼저 확인하고, 최소한의 안전한 변경으로 요구를 충족한다.

### 지시 우선순위

1. 현재 대화에서 사용자가 가장 최근에 내린 명시적 지시
2. 사용자가 승인한 최신 요구사항 명세 및 결정 기록
3. 현재 저장소에서 확인된 실제 구현·데이터 모델·테스트
4. 과거 명령 이력
5. 이 문서의 기본 권장사항

과거 명령 TXT는 **변경 이력**이지, 전 항목을 다시 실행하라는 작업 큐가 아니다. Terrapado 명령 이력은 위쪽이 최신이고 아래쪽일수록 오래된 것으로 취급한다. 같은 주제의 충돌은 가장 최신의 명시적 요청이 우선한다. 완료 여부는 명령 이력만으로 추정하지 말고 소스 코드와 실제 동작으로 확인한다.

### 변경 모드 구분

요청을 다음 모드 중 하나로 분류하고, 혼합 요청이면 각 항목을 별도로 분류한다.

- **IMPLEMENT**: 코드 또는 파일을 실제로 수정한다.
- **INVESTIGATE**: 원인과 근거를 확인하고 보고한다. 수정은 하지 않는다.
- **PROPOSE_ONLY**: 구현 방안을 설계·비교해 보고한다. 사용자 승인 전에는 적용하지 않는다.
- **ASSET**: 이미지, 문서, 프롬프트 등의 산출물을 생성한다.
- **VERIFY**: 기존 구현의 동작과 요구사항 충족 여부를 검사한다.

"검토만", "기획해", "보고부터", "아직 적용하지 마"는 **PROPOSE_ONLY 또는 INVESTIGATE**다. 그런 지시를 구현 권한으로 해석하지 않는다.

---

## 1. 프로젝트 사실 확인

프로젝트를 처음 열거나 구조를 알 수 없는 경우 수정 전에 다음을 확인한다.

1. 현재 작업 디렉터리와 Git 저장소 루트
2. `pubspec.yaml`, Flutter/Dart SDK 제약, 실제 의존성
3. `lib/main.dart`, 앱 초기화, 라우팅/내비게이션 구조
4. `lib/screens/`, `lib/widgets/`, `lib/services/`, `lib/models/`의 실제 구성
5. Provider 또는 실제 사용 중인 상태 관리 방식
6. Firebase 초기화, Authentication, Firestore 모델과 규칙, Messaging/Notifications 설정
7. 기존 테스트, 분석 설정, 관련 코드 및 최근 변경 내용

기존 Harness 문서에 "Web/Capacitor", "React", "TypeScript", `div`, `requestAnimationFrame` 같은 표현이 있더라도, 현재 앱이 Flutter 네이티브라면 그대로 따르지 않는다. **현재 코드와 `pubspec.yaml`이 기술 스택의 기준**이다. 하이브리드 구조가 실제 코드에서 확인된 경우에만 해당 기술을 적용한다.

기존 프로젝트를 새로 생성하거나 전면 재작성하지 않는다. 기존 패턴과 사용자 경험을 유지하며 필요한 부분만 바꾼다. 현재 개발 상태를 확인하기 전에는 어떤 기능을 "구현됨" 또는 "미구현"이라고 단정하지 않는다.

---

## 2. 화면 및 UI 요소 식별 체계

앱 규모는 약 6–20개 화면으로 가정하되 실제 화면 수는 탐색 후 확정한다. 자연어에서 "화면 아래", "우측 메뉴", "그 버튼"을 반복 설명하는 대신 화면 ID와 요소 ID로 참조한다.

### 2.1 ID 규칙

- 페이지: `P01_HOME`, `P02_LOGIN`, `P03_PROFILE_MENU`, `P04_EMOTION_SELECT`, `P05_EMOTION_COMPLETION`, `P20_MY_STRATA`
- 버튼: `BTN01_EMOTION_NEXT`, `BTN02_SAVE`
- 입력: `INP01_EMOTION_MENTION`, `INP02_INTENTION_MENTION`
- 텍스트: `TXT01_GREETING`, `TXT02_GLOBAL_USER_COUNT`
- 카드/목록/탭/모달: `CARD01_EMOTION`, `LST01_EMOTIONS`, `TAB01_INTENTIONS`, `MOD01_DELETE_ACCOUNT`
- 동작: `ACT01_SAVE_EMOTION`, `ACT02_DELETE_ACCOUNT`
- 데이터/서비스: `DATA01_EMOTION`, `SVC01_EMOTION_SERVICE`, `SVC01_AI`

ID는 설명·테스트·요구사항 문서의 안정적인 식별자다. 실제 코드의 모든 위젯에 임의로 `GlobalKey`를 추가하지 않는다. 코드에서 접근·테스트할 필요가 있을 때에만 `Key`, `ValueKey`, `Key` 기반 테스트 식별자 등을 프로젝트 패턴에 맞춰 사용한다. 기존 식별자를 변경해야 할 경우 참조와 테스트를 함께 갱신한다.

> **v4/v5 참고**: Emotion Flow는 **감정 선택(EmotionGridScreen) → 강도+멘션 입력(stage 1) → 통합 완료(stage 2)** 3단계다(§7.1). 문서의 `P10_EMOTION_STAGE_1` … `P15_EMOTION_STAGE_6` 계열 ID는 더 이상 코드 stage와 대응하지 않으므로, 화면 참조는 `P04_EMOTION_SELECT` / `P05_EMOTION_COMPLETION`을 사용한다.

### 2.2 UI 요소 명세 형식

각 화면은 아래 항목만 명확하게 기록한다.

```yaml
page:
  id: P01_HOME
  name: Home
  purpose: 앱의 주요 진입 화면
  entry_conditions: [앱 실행, 로그인 복귀]
  elements:
    - id: TXT01_GREETING
      type: text
      content_rule: 시간대별 인사말
    - id: BTN01_ACKNOWLEDGE_WAVE
      type: button
      action: ACT01_START_EMOTION_FLOW
  states: [initial, loading, empty, error, success]
  navigation:
    - from: BTN01_ACKNOWLEDGE_WAVE
      to: P04_EMOTION_STAGE_1
```

세부 동작을 화면 설명에 장황하게 반복하지 않는다. 화면에서는 `action` ID를 참조하고 동작 정의는 액션 명세 한 곳에 둔다.

### 2.3 액션 명세 형식

```yaml
action:
  id: ACT01_SAVE_EMOTION
  trigger: BTN01_CONFIRM.onPressed
  preconditions: [사용자 인증 상태 확인, 입력값 검증]
  steps: [중복 제출 방지, 데이터 저장, 상태 갱신]
  success: 저장 결과를 UI에 반영
  failure: 원인에 맞는 오류 표시와 재시도 경로 제공
  verification: [정상 입력 저장, 네트워크 오류, 계정 전환]
```

---

## 3. 명령 수신 및 요구사항 정리 절차

사용자 메시지를 받은 뒤 다음을 내부적으로 정리하고, 실제 파일 변경이 필요한 경우 핵심 사항만 간단히 보고한다.

1. **의도 추출**: 무엇이 잘못됐고 어떤 결과를 원하는가?
2. **대상 식별**: 페이지 ID, 컴포넌트 ID, 액션 ID, 데이터/서비스를 찾는다.
3. **모드 분류**: IMPLEMENT / INVESTIGATE / PROPOSE_ONLY / ASSET / VERIFY
4. **이력 대조**: 같은 주제의 가장 최근 요청, 과거 요청, 현재 코드 상태를 대조한다.
5. **충돌 처리**: 최신 명시 지시 우선. 모호한 경우 임의로 충돌을 지우지 말고 근거와 선택지를 보고한다.
6. **영향 범위 평가**: UI만 바꾸는지, 상태/DB/API/권한/알림/계정 흐름에 영향을 주는지 확인한다.
7. **최소 변경 계획**: 수정 대상 파일·예상 동작·검증법을 짧게 정리한다.
8. **구현 및 검증**: 수정 → 정적 검사 → 관련 테스트 → 가능한 경우 앱 실행 확인
9. **결과 보고**: 변경 파일, 실제 확인한 결과, 남은 위험/미검증 항목을 구분한다.

사용자가 즉시 실행을 명시했다면 불필요한 확인 질문으로 작업을 중단하지 않는다. 다만 외부 계정 삭제·데이터 파기·프로덕션 배포·비가역적 변경은 구체적인 승인과 안전한 절차가 필요하다.

---

## 4. 반복 오류 및 디버깅 규칙

같은 오류가 두 번 이상 반복되면 무작위로 다른 코드를 바꾸지 않는다.

1. 이전 시도와 결과를 요약한다.
2. 가능한 원인을 2–3개로 제한한다.
3. 각 원인을 구분할 수 있는 증거(로그, stack trace, DB 문서, UID, 라우트 상태, 권한, 패키지 동작)를 확인한다.
4. 근본 원인을 확인한 뒤 최소 수정한다.
5. 동일 시나리오를 다시 실행해 수정 전후를 비교한다.

Flutter 레이아웃의 `RenderFlex overflow` 및 검정·노랑 경고 패턴은 화면을 임의로 위로 이동하는 방식만으로 덮지 않는다. 키보드 표시 여부, `SafeArea`, `MediaQuery.viewInsets`, `resizeToAvoidBottomInset`, `SingleChildScrollView`, 고정 높이/하단 영역의 제약을 확인해 원인을 수정한다.

`BuildContext`의 생명주기 오류는 `Navigator.pop` 뒤의 context 사용, `StatefulBuilder` 파라미터 shadowing, 비동기 이후 `mounted` 확인 등을 점검한다. 로그가 없는 경우 원인을 사실처럼 단정하지 않는다.

**로그 캡처 방해 이슈(v5)**: `debugPrint` 도배(§9.5 Globe 항목)가 있는 상태에서는 필요한 로그가 버퍼에서 밀려날 수 있다. 분석용 캡처는 항상 `adb logcat` 필터 + 파일 저장(§9.6)을 사용하고, 콘솔 화면 복사에 의존하지 않는다.

---

## 5. Flutter UI/UX 규칙

- 기존 디자인 시스템과 폰트를 먼저 찾고 재사용한다.
- 화면 고정 크기를 무조건 하드코딩하지 않는다. Figma 440×956은 디자인 기준으로 사용하되 Flutter의 논리 픽셀, 제약 조건, 화면 크기, 글자 크기 설정을 고려한다.
- 버튼은 명확한 탭 영역과 접근 가능한 상태(활성/비활성/로딩)를 가진다.
- 레이아웃 위치 조정은 상수 픽셀 이동보다 정렬·제약·여백 구조를 우선한다.
- 애니메이션은 `AnimationController` 등 Flutter 방식으로 구현하고 `dispose()`를 보장한다. 필요하지 않은 무한 애니메이션 및 지속적인 고비용 페인팅을 피한다.
- 감정구체와 지구구체는 가능하면 재사용 가능한 위젯/페인터로 분리한다. 애니메이션 중 텍스트가 이동하지 않도록 레이아웃 공간을 미리 할당한다.
- 화면 회전·작은 기기·큰 글자·소프트 키보드·스크롤·접근성 상태를 고려한다.
- `Capacitor Haptics`, HTML Canvas, `div`, React 훅을 무조건 사용하지 않는다. Flutter에서는 실제 프로젝트의 햅틱/오디오 의존성 및 Flutter API를 사용한다.

### 감정 선택 화면 권장 규칙 (v4 유지)

- 감정 카드 탭은 **선택 상태만 변경**한다. 카드 탭으로 화면을 전환하지 않는다. 전환은 하단 `다음` 버튼으로만 한다.
- 감정을 탭(선택)하는 즉시 해당 감정의 소리/햅틱 미디어 프로파일(`EmotionMediaConfig.forType(...).playAudioAsset()` + `playHaptic()`)을 재생한다.
- 카드 우측 상단의 `?` 칩은 카드 본체 탭과 분리된 개별 제스처여야 한다. `?` 탭은 bottom sheet로 감정 설명을 보여주고, 시트 외부 탭으로 닫힌다.
- `다음` 버튼은 선택 전 비활성(기능·시각 모두), 선택 시 감정 색으로 활성화한다. 일일 제한 도달 시 선택 자체를 막고 스낵바로 안내한다(기존 동작 유지).
- 하단 버튼 영역과 그리드가 함께 화면 안에 들어오도록 스크롤/제약을 설계한다(중첩 스크롤뷰 금지).

### 공용 위젯·재질 (v5.9 신설)

v5.9에서 신설한 공용 위젯 4종이다. 신규 화면·요소는 이 재질/패턴을 우선 재사용한다.

| 위젯 | 파일 | 용도 | 계약 |
|---|---|---|---|
| `showGlassSheet<T>` | `lib/widgets/glass_sheet.dart` | bottom sheet의 유리 재질(C2) | 상단 r24 + `BackdropFilter` blur 18 + tint `#1E1E28@0.78`, barrier black@0.45. `isScrollControlled` 지원 |
| `FrostedHeaderScaffold` | `lib/widgets/frosted_header.dart` | frosted 헤더(C1) | `header`(SafeArea top 포함) + `bodyBuilder(context, headerHeight)` — 헤더 높이 첫 프레임 실측, 실측 전 null이므로 호출부는 폴백값 사용. 본문은 뷰포트 전체에서 시작해 헤더 아래로 스크롤 |
| `StaggerIn`/`staggered()` | `lib/widgets/stagger_in.dart` | 리스트 순차 등장(B3) | index*40ms(최대 480ms) 지연 후 fade+slide **1회성** — 무한 애니메이션 아님(§5 배터리 원칙) |
| `PressableCard` | `lib/widgets/pressable_card.dart` | 프레스 스케일 피드백(B1) | 누름 0.97(140ms) → 떼기 `easeOutBack` 복귀(280ms). **피드백은 `Listener`로 감지(제스처 아레나 비참여)** — 외부 GestureDetector와 중첩돼도 탭 처리가 깨지지 않는다. `onTap`을 직접 주면 이 위젯이 탭도 처리 |

- **전역 트랜지션(B2)**: `main.dart` `pageTransitionsTheme` — android/iOS 모두 `CupertinoPageTransitionsBuilder`. Flutter 3.44부터 이 클래스는 **cupertino 라이브러리 소속**(material 미export) — `show` 절 import로 해결했다. grid→flow 등 커스텀 `PageRouteBuilder`는 슬라이드+패럴랙스 트랜지션을 따른다.
- **스크롤 물성(D4)**: 스크롤뷰·리스트·그리드는 `BouncingScrollPhysics` 통일.
- **백버튼 톤(N7)**: `arrow_back_ios_new` 18px + white@0.08 컨테이너 r12 — 전 화면 표준.
- `CupertinoSwitch`는 `activeTrackColor`를 사용한다(`activeColor`는 v3.24 이후 deprecated — analyze 신규 이슈 방지).

### 5.1 통계 화면 감정 탭 구성 (v5.10 기준)

섹션 순서와 데이터 원칙을 고정시킨다. 신규 섹션 추가 시 이 구성과 대조한다.

1. **AI 해석 카드** — §7.2 통계 인사이트 계약 유지.
2. **감정 기준선 카드 (v5.6)** — **기준선 = 빈도 1위 우세 감정**. 우세 감정명(감정 색) + 전체 중 비중(%) + 기록 존재 날 수 + 최근 14일 우세 감정 비교행. **(v5.10) 전체 기록 강도의 평균·모표준편차 추가**(최근 90일, UNKNOWN 포함, 레코드별 동등 가중, 소수 둘째 자리). "어떻게 하면 행복한지"는 아직 미정의 — 후속 확장은 사용자 명령으로만. (v5.5의 valence 지수 방식은 v5.6에서 폐기)
3. **감정 히트맵** — **90/180/365일 선택**, 기본 90일. 긴 기간은 최소 14px 셀을 유지하는 가로 스크롤 그리드. **(v5.8) 오늘 셀에 흰 외곽선** 표기. **(v5.7) 셀 탭 시 해당 일자 기록 bottom sheet** — 감정 배지·시각·강도·멘션. 기록 없는 셀 탭은 `statsNoRecordsDay` 안내.
4. **감정 분포 도넛 + 감정 분포 변화(누적 영역형)** — 도넛 = 현재 분포, 영역형 = 주 단위(13구간) 비중 변화. 도넛 바로 아래 배치. **(v5.7) 기간 선택기 7/30/90일(기본 90)이 이 섹션 헤더 우측에 있으며(v5.8 배치), 최대 강도 Top 3까지 적용** — 기준선은 90일 고정이고 히트맵은 자체 90/180/365일 선택기를 사용한다.
5. **대응·해소 도넛 + 변화(누적 영역형)** — 동일 구성. **영역형은 해소 아래/대응 위**로 누적한다(v5.10).
6. **외부환경 대응·관계 도넛 + 변화(누적 영역형)** — 동일 구성. (v5.5까지 "생존 위협과 관계", v5.6 라벨 변경 — 멤버는 동일: 슬픔/분노/두려움/기쁨 vs 사랑/싫음)
7. **가장 강했던 감정 Top 3** — peak 일자·멘션 표기(v5.4 유지). 기간 선택기 적용(v5.7).
8. **감정별 평균 강도** — 행 탭 → 해당 감정 강도 변화 그래프 펼침(v5.5 신설, v5.6에서 점 제거 후 연속 곡선). 기간 선택기 적용(v5.7).
9. **이 뒤로 오는 파도 Top 3 / 멘션 패턴 Top 3** — 기존 유지. 기간 선택기 적용(v5.7).

통계 산출은 전부 클라이언트 결정적 순수 함수(`StatsService`)로, AI 호출 없다. 감정 기준선·시계열 비중도 동일 원칙을 따른다.

### 기록 탭과 시각화 위치 (v5.10)

- `MainShell`의 기록은 감정/다짐 두 내부 탭이다. 하단 기록 재탭으로 내부 탭을 번갈아 전환한다.
- 감정 기록은 필터(전체+7종), id 기준 확장 상태, 30건 페이지네이션과 필터 부족분 이어받기를 유지한다. **지층 시각화와 그 위 설명은 삭제되었다.** 이미지 저장은 목록 기준으로 유지한다.
- 다짐 기록은 30건 페이지네이션 목록이다. **나무 시각화와 나무용 별도 조회는 통계로 이동했다.**
- 통계-감정 히트맵 및 통계-다짐 나무의 기간은 각각 90/180/365일(오늘 포함), 기본 90일이다. 이 선택은 기준선/다른 그래프/다짐 30일 지표에 영향을 주지 않는다.
- 통계 나무는 기존 다짐 패턴 자리를 대체하며 `IntentionTree`/`IntentionTreePainter`로 기존 목재·성취도 색과 오래된 기록→최신 순서를 유지한다. 모든 링을 압축해 표시하고 최신 링을 생략하지 않는다.
- 기간 선택은 `HistoryRangeSection<T>`로 실제 날짜 범위 조회·캐시·응답 경합·재시도를 처리한다. 새 컬렉션이나 AI 호출을 추가하지 않는다.

### 표준 페이지 명칭(문서용 기본값)

- `P01_HOME`: Home
- `P02_LOGIN`: Login
- `P03_CREATE_ACCOUNT`: Create Account
- `P04_PROFILE_MENU`: Profile Menu
- `P05_ACCOUNT`: Account
- `P06_SETTINGS`: Settings
- `P04_EMOTION_SELECT`: 감정 선택 (EmotionGridScreen)
- `P05_EMOTION_COMPLETION`: 감정 통합 완료 (EmotionFlowScreen, 최후 stage)
- `P20_MY_STRATA`: 기록 → 감정 / My Strata — 필터·확장 목록·이미지 저장, 지층 시각화·설명 제거(v5.10).
- `P21_TREE_OF_INTENTION`: 기존 다짐 기록 화면 ID. v5.10부터 기록 → 다짐은 목록만 표시하고 나무는 통계 → 다짐으로 이동했다.
- `P30_STATS`: 하단 통계 → 감정/다짐. 하단 통계 재탭으로 내부 탭 전환. 감정은 90일 빈도 기준선+전체 강도 평균/표준편차, 90/180/365일 히트맵, 기존 7/30/90일 그래프. 다짐은 기존 30일 지표와 추이·키워드를 유지하며 기존 다짐 패턴 자리에 90/180/365일 다짐의 나무를 표시한다.


실제 라우트·화면 구조가 다르면 이 번호를 강요하지 말고 기존 구조를 찾아 ID 레지스트리에 매핑한다.

---

## 6. Firebase, 계정, 데이터 및 개인정보

### 6.1 인증과 사용자별 상태

- 감정 기록과 다짐 기록은 인증된 `uid`와 연결되어야 한다.
- 계정 전환/로그아웃 시 메모리에 남은 이전 계정의 데이터·캐시·Provider 상태를 즉시 비우고, 새 UID 기준으로 다시 구독/조회한다.
- 앱 프로세스가 살아 있는 상태에서 계정을 전환해도 이전 계정의 기록이 노출되어서는 안 된다.
- 계정 화면에서 재인증이 필요한 작업은 Firebase SDK의 지원 절차를 사용하고, 실패 원인을 노출 가능한 범위에서 구분한다.
- 같은 이메일의 여러 로그인 방법을 연결할 때 Firebase가 요구하는 유효한 credential과 재인증 단계를 확인한다. 인증 수단을 확보할 수 없는 경우 무조건 자동 연결을 시도하거나 다른 계정에 접근시키지 않는다.

### 6.2 Firestore 데이터 원칙

#### 컬렉션 목록 (v5 기준, 8개)

`emotions` / `emotion_globe` / `resolutions` / `users` / `app_stats` / `fcm_tokens` / `deletion_log` — 그 외 **폐기**: `daily_emotion_stats`, `daily_user_stats`(아래 참고).

- `daily_emotion_stats`/`daily_user_stats`는 **이전 버전의 잔재로 2026-10-21 폐기**되었다.
  - `daily_emotion_stats`: 쓰기만 존재, 읽기 consumer 없음(dead code)이었음.
  - `daily_user_stats`: 읽기 consumer가 있었으나 전부 emotion_globe 기반 UID dedup으로 대체 가능했음. 또한 increment 방식은 "고유 사용자 수" 주장과 달리 레코드별 중복 증가 버그가 있었다.
  - 클라이언트 호출은 전부 제거 완료(`lib/services/firestore_service.dart`, `lib/providers/emotion_provider.dart`). DB 컬렉션 자체는 사용자가 Firebase Console에서 수동 삭제한다. **以后 코드에서 이 컬렉션들을 다시 참조하지 않으며, 새 write로 재생성되어서도 안 된다.**

기존 프로젝트 문서에 정의된 다음 목적을 유지하되, 실제 코드·규칙과 일치하는지 먼저 확인한다.

- `emotions`: 개인 감정 기록. 메모와 AI 피드백 등 개인 세부정보 포함 가능, 본인만 접근.
- `emotion_globe`: 전 세계 오늘의 감정 시각화를 위한 최소 데이터. `note`, `aiFeedback` 등 개인 세부정보를 저장하지 않는다.
- `saveEmotion()` 흐름은 개인 기록 저장 성공을 우선 조건으로 하고, 글로벌 시각화 복제 실패 시 이를 조용히 성공으로 오인하지 않도록 로그/재시도 정책을 둔다.
- 공개/공유 컬렉션의 쓰기·읽기는 Firestore Security Rules에 의해 제한한다. 앱의 `try/catch`로 보안 규칙 위반을 숨기지 않는다.
- 날짜 집계는 사용자 시간대와 서버 타임스탬프의 기준을 명시한다.
- **주의 (v5)**: `firestore.rules` 파일이 리포에 없다. 규칙 정합성 검증이 필요하면 Firebase Console에서 확인한다.

#### 글로벌/동일 감정 카운트 규칙 (v5 갱신)

- 글로벌 카운트는 **오늘 레코드 수가 아니라 오늘 한 번 이상 기록한 고유 사용자 수**를 계산한다(중복 UID 제거).
- 완료 화면의 "나와 같은 감정 수"도 동일 원칙을 따른다: **오늘 같은 감정 타입을 기록한 고유 사용자 수**.
- 데이터 소스는 **emotion_globe로 단일화**되었다(v5). `EmotionProvider`가 `globalTodayEmotions`에서 클라이언트 산출(추가 쿼리/비용 없음)하며, 파생값이므로 저장하지 않는다. 저장 후 첫 기록일 때는 인메모리 증분(`_dailyUserCount++`)으로 즉시 반영한다.
- 현재 사용자가 이번에 저장할 신규 기록은 저장 전 단계이므로 카운트에서 제외된다(사용자에게 "함께하는 다른 영혼 수"로 표시).
- DB 마이그레이션이나 일괄 삭제는 읽기·쓰기 범위, 백업/복구 가능성, 보안 규칙을 확인한다.

### 6.3 계정 삭제

계정 삭제는 단순 로그아웃과 구분한다. 사용자 재인증, 개인 문서/공유 문서/하위 컬렉션 정리, 관련 파일이 있다면 파일 정리, Authentication 계정 삭제, 실패 복구/로그 처리의 순서를 설계한다 (`deleteAllUserData`는 users/emotions/emotion_globe/resolutions/fcm_tokens를 batch 삭제한다 — v5 기준 동작). 클라이언트 SDK와 보안 규칙으로 안전하게 수행할 수 없는 부분은 Cloud Functions 또는 신뢰된 서버가 필요한 이유를 보고한다. Firebase Console에서 할 수 있다는 이유만으로 REST API를 통해 파괴적 작업을 즉흥적으로 수행하지 않는다.

### 6.4 데이터 최소화 및 Analytics

- 원문 감정 멘션·다짐 내용은 Analytics 이벤트나 로그에 넣지 않는다.
- 전환율, 페이지 방문, 체류시간, 텍스트 글자 수처럼 필요한 최소 지표만 기록하고 개인정보 정책과 실제 수집 항목을 일치시킨다.
- API 키/비밀 값은 소스 코드와 커밋에 넣지 않는다. `.env.json` 또는 프로젝트에서 정한 안전한 주입 방식을 사용하고 `.gitignore`를 확인한다.
- 클라이언트에 포함된 Firebase 설정값이 공개용이어도 Security Rules, App Check, 인증 정책이 안전하다고 가정하지 않는다.

---

## 7. Emotion Flow와 AI 피드백 계약

현재 코드와 승인된 최신 명세를 확인한 다음 구현한다. Emotion Flow는 **감정 선택 → 입력 → 통합 완료 3단계**다(v4 확정 유지).

### 7.1 화면 구조

**1단계 — 감정 선택 (`EmotionGridScreen`, `P04_EMOTION_SELECT`)**

| 요소 | 동작 |
|---|---|
| 감정 카드 본체 탭 | **선택만**(영구 선택 하이라이트) + 즉시 소리/햅틱 미디어 프로파일 재생. 화면 전환 없음 |
| 카드 우측 상단 `?` 칩 탭 | 감정 설명 bottom sheet(구 stage 0 설명 화면 대체) |
| 하단 `다음` 버튼 | 선택 전 비활성 → 선택 시 감정 색 활성 → `clearCache()` 후 입력 화면 push(stage 1, totalStages 2) |
| 일일 제한 | 선택 탭 시 확인, 도달 스낵바 기존 동작 유지 |

**2단계 — 강도 + 멘션 입력 (`EmotionFlowScreen` stage 1)**

- 강도 1–10 슬라이더(상단, `mild`/`intense` 라벨, 강도 비례 햅틱) + 선택적 멘션(최대 300자, 하단) + Skip/Confirm 버튼.
- 진입 햅틱: `emotionTypeHaptic`. 오브 크기 170(×0.7). `tapToDive` 힌트 숨김.
- 배경 탭 = 키보드 내림(`FocusScope.unfocus`)만, 진행은 Confirm/Skip 버튼으로만 한다.
- Confirm/Skip 시: 멘션 캡처 → `generateAllTexts` **1회** 트리거(unawaited) → stage 2 pushReplacement.

**3단계 — 통합 완료 (`EmotionFlowScreen` stage 2 = 최후 stage)**

- content: 진입 햅틱 `success` + **지구구체**(오브 180, ×0.5) + 오늘 나와 같은 감정 **고유 사용자 수** + AI 3문장(empathy/release/guidance) + `tapToDive` 힌트.
- **스며듦(seep) 효과**: 화면 **첫 탭 1회만** — 선택 감정 색 입자가 구체 상단에서 하강해 흡수되며, 표면 반응 링과 코어 글로우(감정 색 틴트)가 재생. 재생 중 추가 탭 무시. **효과 완료 후 `onComplete`로 저장**. 파일 내부 `_EmotionSeepOverlay`/`_EmotionSeepPainter` (고정 시드, controller `dispose()` 보장).
- `onComplete`는 감정 저장 → 알림 재예약 → 시나리오 분기(저녁 복기 / 다짐 입력 / HOME) 순서를 유지한다.
- 진입 Analytics: `logViewAiFeedback` 완료 화면에서만 기록. lastScreen: `emotion_flow_stage_${stage}`.
- AI 텍스트 미도착 시 완료 화면은 스켈레톤(`_poeticLoadingSkeleton`) 표시 — 3문장 전부 도착 시 `allReady`로 전환된다(§7.6 지연 이슈와 직결).
- **진입 게이트 대기(v5.7/v5.8)**: 진입 시 고정 지연 후 UI 전환 — 기본 2초, **AI가 initState 시점에 이미 준비돼 있으면 800ms(`_readyDelayFast`)로 단축(v5.8)**.
- **AI 3문장 스태거 표시(v5.7)**: 3문장을 동시 노출하지 않고 `TypingText.startDelayMs`로 **0/800/1600ms 순차 등장**시킨다.

### 7.2 AI 텍스트 인덱스

- `AiService.generateAllTexts` → `{3: empathy, 4: release/rootCause, 5: guidance}` (각 60자 미만).
- 목록용 저장 요약(`aiFeedback`)은 `[guidance]`(index 5)를 사용한다.
- **호출은 입력 화면(stage 1)의 Confirm/Skip 시점에 1회** 트리거한다. 이때 실제 강도(`_intensity`)와 멘션을 전달한다. `다음` 버튼에서는 이전 플로우 캐시 오염 방지를 위해 `AiService.clearCache()`만 호출한다. 완료 화면은 공유 캐시(`AiService.lastGeneratedTexts`) 회수 또는 in-flight dedup join으로 **동일 1회**를 유지한다. 화면마다 재호출하지 않는다.
- in-flight dedup과 공유 캐시를 유지하며, 불필요한 추가 호출을 만들지 않는다.
- **v5.2**: `generateAllTexts`에 옵션 `mention` 파라미터가 있다. UNKNOWN 감정 + 멘션 입력 시에만 멘션을 분류 근거로 프롬프트에 포함한다(§7.5 예외, v5.2). 그 외(일반 감정, UNKNOWN 무멘션)는 멘션을 프롬프트에 넣지 않는다. SDK 내부 파이프라인 변경 없이 화면 호출부(stage 1 `_onTap`/stage 2 `_loadAllAiTexts`)만 `_mention` 값을 넘긴다.
- **v5.3**: 통계 화면 전용 `generateStatsInsight({kind, summary, languageCode})`가 별도 경로로 존재한다. `summary`는 `StatsService.buildEmotionDigest()/buildIntentionDigest()`가 만드는 결정적 영문 digest(집계 수치·키워드 토큰만)이며, Emotion Flow 3문장 계약과 완전히 분리된 호출이다. 캐시 키 = `종류|언어|날짜|데이터 해시` — 같은 날 같은 데이터면 재호출하지 않는다. digest에 사용자 원문 문장·다짐 내용 전문을 넣지 않는다(§7.5 기본 규칙 유지, 토큰 이름 인용만 허용). 통계 경로의 throttle은 fallback 즉시 반환 대신 남은 시간 대기 방식이다.
- **v5.7**: throttle 처리 통일 — Emotion Flow 경로도 통계 인사이트 경로와 동일하게 3초 창에서 **fallback 즉시 반환 대신 남은 시간만큼 대기 후 호출**로 변경(§7.6 후보 1 적용). 영어 fallback 즉시 노출이 원천 차단된다.

### 7.3 "나와 같은 감정" 고유 사용자 수

- 완료 화면의 "오늘 나와 같은 감정을 가진 사람 수"는 **오늘 같은 감정 타입을 기록한 고유 사용자 수**(userId 기준 중복 제거)다. 레코드 개수나 다중 기록 수가 아니다.
- v5부터 데이터 소스는 **emotion_globe 단일**이다: 이미 로드된 공개 데이터(`EmotionProvider.globalTodayEmotions`)에서 클라이언트 산출(추가 쿼리 없음). 일별 stats 컬렉션 기반 Lireads/increment 경로는 폐기되었다(§6.2).
- 현재 사용자의 신규 기록은 저장 전이므로 제외된다.
- 정적 라벨은 l10n 키 `soulsShareThisWave`를 사용한다.

### 7.4 AI 감정 피드백 다국어

- AI 감정 피드백은 앱의 **현재 로케일 언어**로 생성한다.
- `generateAllTexts(..., String languageCode)`에 호출부(입력 화면 stage 1의 Confirm/Skip 핸들러)가 `Localizations.localeOf(context).languageCode`를 전달한다.
- 프롬프트에 `OUTPUT LANGUAGE` 지시를 넣어 **본문만** 대상 언어로 생성하고, `[empathy]/[release]/[guidance]` **라벨은 영문 유지**(앱이 파싱하는 키).
- 언어 매핑(`_languageName`): ko→Korean(한국어), ja→Japanese(日本語), zh→Chinese(简体中文), es→Spanish(Español), de→German(Deutsch), fr→French(Français), 그 외 English.
- 정적 UI 문자열은 ARB(`lib/l10n/app_*.arb`, en/ko/ja/zh/es/de/fr)와 `AppLocalizations`를 사용한다(v4에서 `next` 키 7개 언어 추가 완료). `flutter gen-l10n`으로 재생성한다.

### 7.5 AI 응답 품질 기본값

- 문장은 선언형으로 작성하며 질문형·"How can I help you?"류의 되묻는 답변을 금지한다.
- 전지적·초월적·차분한 톤을 사용하되 감정을 실제로 인정하고, 단정적인 진단이나 과장된 확신은 피한다.
- 사용자가 입력한 개인적 세부사항을 불필요하게 그대로 되풀이하지 않는다(멘션 원문은 프롬프트에도 넣지 않는다).
- 문장 수 제한(최신 기준 **60자 미만**)을 준수한다.
- `[empathy]`, `[rootCause]`, `[guidance]`, `[summary]`를 한 요청으로 생성하는 흐름을 유지하고, 필요 없는 호출을 추가하지 않는다.
- "DB를 바탕으로 Stage 3/4 문구 생성"은 **보고만 하고 승인 전에는 적용하지 않는다**.

### 7.6 AI 피드백 지연·영어 출력 이슈 (v5 조사 기록 — 미해결)

사용자 보고(2026-10-21): (1) 완료 화면 AI feedback 표시가 느리다 (2) 영어로 나온다.

**코드상 정상 확인된 것**: languageCode 파이프라인 — Confirm/Skip 핸들러가 `Localizations.localeOf(context).languageCode`를 전달(emotion_flow_screen.dart), 프롬프트에 `OUTPUT LANGUAGE` 지시 포함(ai_service.dart `_languageName` 매핑). 즉 'ko' 요청이면 모델에게 한국어 생성 지시가 들어간다. 영어가 표시됐다면 **fallback 경로**로 한정 추정된다.

**영어 출력 원인 후보 (판별 전, 정확도 우선순위 순)**:

1. **Throttle 3초 창 (최우선 후보 → v5.7 적용 완료)** — 기존에는 `ai_service.dart`에서 `elapsed < _throttleSec`면 **영어 fallback을 즉시·무알림 반환**했다(직전 AI 생성 후 3초 내 새 감정 Confirm에서 재현). **2026-11-12(v5.7)에 "남은 시간 대기 후 호출"로 수정되어 후보 1은 적용 완료**다. 영어 재현 시 잔여 후보는 아래 2·3이다.
2. **파싱 실패 시 누락 stage 영어 채움** — `_parseResponse`가 라벨을 놓치면 누락 stage만 영어 fallback(`_fallbacks`)으로 채움. 세 문장 중 일부만 영어인 경우.
3. **API 키 미설정** — 이 경우 항상 영어+즉시이므로 "느리다"는 보고와 상충. 가능성 낮음.

**지연 원인 후보**:

- stage 2는 3문장 전체 도착까지 스켈레톤 표시 → generation 완료 시간이 그대로 노출 대기시간.
- 프롬프트 ~2.5천자, model `gemini-3.1-flash-lite`, `maxOutputTokens 300` — 실측 필요.
- 503/429 재시도 시 +1.5초 + 2차 호출.
- `generateContent`에 **요청 타임아웃 없음** — 네트워크 정체 시 무한 hang 가능.
- 저장은 unawaited이므로 저장 지연은 아님. 지연감은 전부 텍스트 표시 대기.

**확증 방법**: §9.6 캡처 절차로 기기 로그에서 `[AiService]` 시리즈 확인 — `⏱ Throttled`→후보1·`⚠️ Some stages used fallback text`(+raw response)→후보2·`⛔ GEMINI_API_KEY not configured`→후보3·`✅ Generated all 3 texts` 소요시간→지연 실측.

**제안 후보 (승인 전 미적용 — throttle 항목은 v5.7에 적용됨)**: ~~throttle을 "남은 시간 대기 후 실행"으로 변경 또는 제거~~(v5.7 적용) / `_fallbacks` 다국어화 또는 실패 안내 표시 / `generateContent` 타임아웃(15–20초) 추가 / 생성이 실측상 느리면 프롬프트 축소·스트리밍 검토(§7.5 승인 필요).

**판별 상태**: 후보 1(throttle 즉시 fallback)은 2026-11-12(v5.7)에 적용 완료. 영어 출력이 여전히 관측되면 후보 2·3과 지연 후보를 재판별한다. 1차 로그(`C:\coding\error message.txt`)는 홈 지구구체만 캡처 + `[Globe]` 도배(§9.5)로 AI 시점 미포함. §9.6 절차로 재수집이 전제다.

---

## 8. 알림, 딥링크 및 앱 생명주기

### 8.1 일일 리마인더 (v5.3 개편)

- **시각**: 기본 **08:00 / 12:00 / 19:00** — 감정입력을 권장하는 알림. 채널 `terrapado_daily`. **(v5.7) 각 시각을 홈 메뉴 타일의 시각 칩에서 개별 변경 가능** — SharedPreferences `reminder_time_$i`("HH:mm") 저장, 저장 즉시 재예약. **(v5.9) 선택기는 iOS식 휠 피커(유리 시트 내 시/분 `CupertinoPicker`, `_WheelTimePicker`)** — 저장·재예약 로직(`TimeOfDay` 파이프라인)은 변경 없음. 스마트 스킵·exact 폴백 등 아래 규칙은 변경 후에도 동일 적용.
- **토글**: SharedPreferences `reminder_enabled`, **기본값 켜짐**. 홈 '三' 메뉴의 스위치 타일에서 변경한다. OFF 시 예약된 리마인더만 취소한다(id 0..2) — FCM 알림·상시(persistent) 알림은 토글과 무관하게 유지된다.
- **게이트 단일 지점**: `NotificationService.scheduleAllReminders()`가 진입 시 토글을 확인한다. 모든 기존 호출부(main 스플래시, auth 로그인, 감정 저장 후, 다짐 저장 후)는 호출부 변경 없이 이 게이트를 통과한다.
- **스마트 스킵 유지(45분 창, v5.4)**: 해당 알림 시각 직전 45분 이내 마지막 감정 기록이 있으면 그 회차만 자제한다. 기존 3시간 창은 "아침 9:15 기록이 12:00 알림을 소멸"시켜 팝업 부재 보고의 1차 원인이었다.
- **exact alarm (v5.4)**: Android 12+에서 `SCHEDULE_EXACT_ALARM` 매니페스트 권한 + `exactAllowWhileIdle` 사용. `canScheduleExactNotifications()`가 false면 `inexactAllowWhileIdle`로 폴백(사용자가 OS 설정에서 해지 가능, 로그 `exact: false`로 확인).
- **예약 갱신은 id 0..2 전용 취소 (v5.4)**: `scheduleAllReminders()`는 리마인더만 `_cancelReminders()`로 교체한다 — 전체 취소(`cancelAll`)는 상시(persistent, id 9999) 알림까지 지우는 v5.3 회귀였다.
- OS 알림 권한이 거부된 상태에서 ON하면 권한을 재요청하고, 거부되면 `notificationDeniedHint` 스낵바로 기기 설정 경로를 안내한다.
- 알림 본문 텍스트는 기존 규칙대로 코드 내 영문 고정 문자열이다(로케일 미반영, 기존 동작 유지).

### 8.2 주간 요약 알림 (v5.7 신설)

- **시각**: 매주 일요일 19:00, `DateTimeComponents.dayOfWeekAndTime` 반복. 채널 `terrapado_weekly`, 알림 **id 10**.
- **본문**: 최근 7일 기록의 최빈 감정(건수 기준)에 대응하는 영문 고정 요약 문구 — 기록 없으면 준비 안내 문구. §8.1과 동일한 영문 고정 규칙(로케일 미반영).
- **토글**: SharedPreferences `weekly_summary_enabled`, **기본값 켜짐**. 홈 메뉴에 리마인더 토글과 별도의 스위치 타일(`_WeeklySummaryToggleTile`)을 둔다. OFF 시 id 10만 취소 — 일일 리마인더·상시 알림 보존.
- **재예약 지점**: 스플래시(자동 로그인 후), 감정 저장 직후(emotion_grid), 토글 ON(userId 있을 때). **메시지는 예약 시점 스냅샷** — 주 중 재예약 전까지 내용이 갱신되지 않는 한계를 문서화해 둔다.
- 스케줄링 실패는 조용히 로그만 남긴다(진행 흐름 방해 금지).

### 8.3 홈 위젯 — Quick Record (v5.7 신설, Android 전용)

- **구성**: 네이티브 AppWidget(`TerrapadoWidgetProvider`) 4×1 — 앱명 / 최근 감정 한 줄 / 기록 유도 문구. `res/layout/terrapado_widget.xml` + `res/xml/terrapado_widget_info.xml`(updatePeriodMillis=0, 폴링 없음) + 배경 drawable. `home_widget` 패키지는 사용하지 않는다 — **MethodChannel `com.terrapado/widget`**으로 Dart→Kotlin 푸시(`WidgetHelper.kt`가 SharedPreferences `terrapado_widget`에 content_text/emotion_color 저장 후 모든 위젯 인스턴스 갱신).
- **탭 동작**: 위젯 전체 영역 탭 → `com.terrapado.ACTION_OPEN_GRID` 인텐트 → 기존 알림 Log 흐름(`pendingOpenGrid` 플래그 + `onLogButtonTapped` 채널)을 재사용해 감정 선택 화면(P04_EMOTION_SELECT) 진입. 로그인 상태 유지 로직 공유.
- **갱신 경로**: `WidgetService.updateLatestEmotion()` — 상시 알림(showPersistentEmotionNotification) 호출 지점과 동일한 EmotionProvider 내부에서 호출(기록 저장·로드·제한 도달 모두). Dart 레이어에서 최신 감정을 푸시하는 유일 경로다.
- **제한**: 위젯 문구는 네이티브 strings로 l10n 미적용(§8.1 영문 고정 취지), 배경은 앱 다크 톤 고정. iOS 위젯은 미구현.

### 8.4 검증 시나리오

- OS 알림과 앱 아이콘이 같은 Flutter 앱 인스턴스/네비게이션 상태로 연결되도록 Android intent/deep-link 처리와 초기 라우팅을 검증한다.
- 알림으로 진입해도 세션이 유지되어 있으면 로그인 상태가 유지되어야 한다. 인증을 우회해서는 안 된다.
- OS가 기본 알림 표시 형태를 제어하는 영역(접힘/확장 여부 등)은 앱의 완전한 강제 제어가 가능한지 Android API와 OS 제한을 확인한 후 보고한다. "항상 펼쳐짐"이 보장되지 않으면 그 제한과 가능한 최선의 동작을 설명한다.
- 알림 권한이 필요한 경우 현재 Android 버전에 맞게 요청하고, 권한 거부/재요청/설정 링크 경로를 검증한다.
- 감정 제한 횟수 도달 시 알림 액션과 홈 화면 버튼의 상태가 같은 서버/로컬 기준을 사용하도록 한다.
- 앱 종료/백그라운드/프로세스 재생성/딥링크/로그아웃 상태의 각 시나리오를 별도로 시험한다.

---

## 9. 실행·검증·보고

### 9.1 허용·금지 기준

- Flutter/Dart 코드 분석, 테스트, `flutter analyze`, 관련 테스트 실행, 필요 시 `flutter run`을 사용한다.
- `flutter clean`은 기본 금지다.
- `adb install` 및 release APK 생성은 사용자가 담당한다는 기존 약속을 유지한다.
- 푸시 알림 Cloud Functions 배포와 프로덕션 데이터 조작은 명시적 요청·권한이 없으면 실행하지 않는다. Firebase Console에서의 컬렉션/문서 수동 삭제도 사용자 담당이다(v5).
- 대규모 삭제/이동, Git 이력 변경, commit/push는 요청이 명시된 경우에만 수행한다.
- 백그라운드 에이전트 위임과 비동기 작업을 사용하지 않는다. 파일 탐색·구현·QA는 현재 작업에서 동기적으로 수행한다.
- 문서/프롬프트 변경 요청 자체가 목적이면 해당 문서 파일만 갱신하고, 관련 없는 코드는 건드리지 않는다.

### 9.2 완료 기준

수정 후 가능한 항목을 실행한다.

1. `dart format` 또는 프로젝트에 맞는 formatter
2. `flutter analyze`
3. 변경 영역에 관련한 단위/위젯 테스트
4. 가능한 경우 실제 Android 디바이스의 `flutter run` 및 로그 확인
5. 저장/계정전환/네트워크 실패/키보드 표시 등 해당 기능의 회귀 시나리오

테스트를 실행하지 못했으면 통과했다고 말하지 않는다. "수정함", "정적 검사 통과", "기기에서 확인", "미검증"을 별도로 보고한다.

### 9.3 최종 보고 형식

```text
결과: 완료 / 부분 완료 / 원인 분석만 / 제안만
대상: [Page IDs / Component IDs / Action IDs]
원인 또는 핵심 판단: ...
변경 파일: ...
검증: 실행한 명령과 실제 결과
미검증/위험: ...
사용자 조치 필요: ...
```

문제 원인을 입증하기 전에는 "원인은 X"라고 단정하지 않는다. 추정과 관측 결과를 명시적으로 구분한다.

### 9.4 기기 테스트 절차

- 기기 연결 확인: `flutter devices` (또는 `%LOCALAPPDATA%\Android\Sdk\platform-tools\adb.exe devices -l`).
  - 확인된 테스트 기기(2026-10-18): `SM N986N` (Galaxy Note 20 Ultra), Android 13 (API 33), arm64, device id `R3CN80W6YLK`.
- 기기 실행: 프로젝트 루트에서 `flutter run -d <deviceId>` (예: `flutter run -d R3CN80W6YLK`).
  - 이 명령이 현재 소스 기준 **디버그 빌드를 새로 만들어 설치·실행**한다. 실행 중 `r`=핫 리로드, `R`=핫 리스타트, `q`=종료.
- **주의**: `build\app\outputs\...\app-debug.apk` 같은 기존 산출물은 과거 빌드일 수 있다. 최신 변경을 보려면 반드시 `flutter run`으로 새로 빌드한다. `flutter clean`은 금지.
- `adb install` 수동 설치 및 release APK 생성은 사용자 담당이다(§9.1).

### 9.5 알려진 기존(무관) 이슈 (v5 시점)

이번 변경과 무관하게 남아 있는 항목이다. 사용자가 명시적으로 요청하지 않는 한 수정하지 않고, 보고 시 "기존 이슈"로 구분한다.

- 전체 `flutter analyze` 기존 이슈 2건(v5.10 최종 확인):
  - `lib/providers/app_provider.dart` — unnecessary_import
  - `lib/widgets/emotion_globe_painter.dart` — unused element `_drawEmotionDots`
- `test/widget_test.dart` — Flutter 기본 카운터 스모크 테스트(기존부터 통과하지 않음, Firebase 초기화 없음으로 실패). Emotion Flow를 커버하지 않음.
- **[신규 v5] 지구구체 Painter 로그 도배**: `emotion_globe_painter.dart`의 `debugPrint('[Globe] Drawing TEXTURE sphere (2048x1024)')`가 매 paint 프레임 기록되어 logcat을 도배한다. 저장 제안(PROPOSE_ONLY) 상태 — 로깅 완화(최초 1회 또는 샘플링)를 제안했으며, 로그 분석 품질 때문에 우선순위가 있다.
- **[신규 v5] firestore.rules 리포 미존재**: 보안 규칙이 리포에 없다. 필요 시 Firebase Console에서 확인한다.

### 9.6 기기 로그 캡처 절차 (v5 신설)

flutter run 콘솔을 복붙하지 않는다 — 길이 잘림 + PSReadLine 예외 + 로그 도배로 유실된다. 위 §4참고.

**기본 절차**:

```powershell
# flutter 채널만 필터링하여 파일에도 동시 기록
& "$env:LOCALAPPDATA\Android\Sdk\platform-tools\adb.exe" logcat -v time -s flutter | Tee-Object -FilePath C:\coding\flutter_log.txt
```

- 캡처 상태에서 기기에서 시나리오를 재현한다. AI 이슈 판별이라면: **감정 기록 2회 연속**(첫 완료 직후 곧바로 두 번째) — 지연 케이스(throttle 영어)와 정상 생성 케이스를 한 번에 잡는다.
- 캡처 후 `flutter_log.txt`에서 `[AiService]`/`[EmotionFlow]` 라인만 검색해 분석한다.
- 주의: 앱을 백그라운드로 돌리면 Surface 세션이 끊겨 캡처 구간이 얼기쉽다(2026-10-21 1차 로그가 그렇게 실패했다). 재현 중에는 앱을 포그라운드로 유지한다.

---

## 10. 시작 시 수행할 작업

기존 저장소가 열린 상태에서 사용자가 새로운 명령을 주면:

1. 현재 명령을 과거 이력과 대조하되, 전체 과거 이력을 자동 재실행하지 않는다.
2. 관련 페이지/요소/액션/서비스 파일을 찾아 실제 구현을 확인한다.
3. 명령이 검토 전용인지 실행 요청인지 판별한다.
4. 수정이 허용된 경우에만 최소 변경 후 검증한다.
5. 명령 이력 또는 Harness 문서 변경 자체가 요청된 경우, 해당 파일만 갱신하고 관련 없는 코드 변경을 하지 않는다.

**시작 기본 지침**: "현재 저장소와 관련 코드를 먼저 읽고, 최신 사용자 요청과 과거 이력의 충돌을 정리하라. 사용자가 승인하지 않은 제안 전용 사항은 구현하지 말라. 확인한 근거와 실제 검증 결과만 보고하라."

---

## 부록 A. v5 구현 기록 (2026-10-21)

- **변경 파일**:
  - `lib/services/firestore_service.dart` — daily stats 전용 5개 메서드 제거: `incrementEmotionCount`, `getEmotionCount`, `getTotalEmotionCount`, `incrementDailyUserCount`, `getDailyUserCount`
  - `lib/providers/emotion_provider.dart` — `getDailyUserCount` 읽기 제거 → 오늘 고유 사용자 수를 emotion_globe UID dedup으로 직접 산출(기본 경로화); 저장 시 `incrementEmotionCount`/`incrementDailyUserCount` DB 호출 제거. 인메모리 `_dailyUserCount++`(첫 기록 반영)는 유지.
- **변경 이력 요약**: 사용자가 `daily_emotion_stats`/`daily_user_stats`를 이전 버전 잔재로 판정 → Firebase에서는 수동 삭제(사용자 담당), 프로그램 코드 호출은 이 세션에서 전부 제거.
- **검증**: `flutter analyze` — 신규 오류 없음, §9.5 기존 경고 3건만 존재.
- **미검증/남은 과제**:
  1. 실기기 동작(홈/완료 화면 "나와 같은 감정 수" 표시는 코드 레벨 로직 동등 확인만 수행)
  2. §7.6 AI 피드백 지연·영어 이슈 — 원인 후보 판별 미완, §9.6 캡처 재수집 필요
  3. 지구구체 Painter 로그 도배 — 제안 상태(미적용)

---

## 부록 B. 2026-10-21 명령 이력 (시간순)

> 이 세션의 사용자 요청·승인·구현·정정 기록이다. 같은 주제의 후속 명시 지시가 이 항목과 충돌하면 항상 가장 최신 지시가 우선한다(§0 지시 우선순위).

1. **[질문/INVESTIGATE] Firestore 컬렉션 목록 확인** — "db가 지금 firebase에 몇가지 있지?"
   - 코드 grep으로 9개 컬렉션 확인(users, emotions, emotion_globe, resolutions, app_stats, daily_emotion_stats, daily_user_stats, fcm_tokens, deletion_log). `firestore.rules` 리포 미존재도 이 시점 확인.
2. **[질문/INVESTIGATE] daily stats 사용 여부** — "daily_emotion_stats와 daily_user_stats 가 있는데 이걸 지금 사용하는 db니? 안쓰면 삭제해도 되니?"
   - 확인: `daily_emotion_stats`는 쓰기만(읽기 dead code), `daily_user_stats`는 쓰기+읽기 활성(홈 "나와 같은 감정 수" 표시). v4 §6.2 규칙(emotion_globe UID dedup)과의 경계에 있는 레거시 카운터로 보고. 교체안을 PROPOSE_ONLY로 제시.
3. **[가정 질문/INVESTIGATE] Firebase 수동 삭제 시 영향** — "firebase에서 삭제하면 어떻게 돼? 아직 삭제하라는 의미는 아냐"
   - 보고: UI는 거의 유지되나 fallback 경로로 전환; Firestore 특성상 다음 write에서 컬렉션 재생성됨; "완전 제거"는 코드 write 경로 수정이 동반이어야 함.
4. **[IMPLEMENT 승인] 프로그램 코드에서 daily stats 호출 제거** — "내가 firebase에서 수동 삭제할거야. 너는 프로그램에서 이것을 호출하는게 있는지 보고, 만약 있다면 삭제해."
   - 구현: 위 부록 A 기록대로 2파일 수정, `flutter analyze` 통과(기존 3건만). UI의 고유 사용자 수는 emotion_globe UID dedup 단일 소스로 전환. Firebase Console 수동 삭제는 사용자 담당(진행 여부 세션 시점 기준 확정 아님 — 재생성 방지 목적상 코드 정리가 선행됨).
5. **[INVESTIGATE] AI 피드백 지연 + 영어 출력** — "감정입력 완료 후 ai feedback이 너무 오래 걸려. … 영어로 나오네. 점검해봐"
   - languageCode 파이프라인 정상 확인. 영어 출력은 fallback 경로로 한정 추정(throttle 3초 창 최우선 후보). 지연은 스켈레톤 대기 구조 + 타임아웃 부재 + 재시도 비용 후보 보고. 모든 수정은 승인 전 미적용(§7.6).
6. **[로그 제공] 재분석** — `"C:\coding\error message.txt" 이게 로그 전부인지는 정확하지 않ㅇ므.`
   - 분석: 감정 기록/AI 시점 미포함(홈 지구구체만 캡처), `[Globe]` 매 프레임 도배, 백그라운드 전환 후 세션 종료. `[AiService]` 라인 0건 → 판별 불가. §9.6 캡처 절차 신설 + Globe 도배 완화 저장 제안(PROPOSE_ONLY). 사용자에게 재캡처 방법 제시(adb logcat -s flutter + Tee-Object, 또는 에이전트 백그라운드 캡처 옵션) — 선택 보류.
7. **[ASSET] Harness 문서 갱신** — "하네스 프롬프트 v5 업데이트해. 오늘은 여기까지 할게."
   - 이 문서 v5.0 작성. §6.2/§7.3 컬렉션 폐기 반영, §7.6 조사 기록, §9.5 기존 이슈 추가, §9.6 캡처 절차 신설.

### 세션 확정 사항 요약 (v5 기준 구분 가능한 항목)

| 항목 | 결정 |
|---|---|
| `daily_emotion_stats` | 폐기 — 클라이언트 호출 전부 제거, DB는 사용자가 수동 삭제 |
| `daily_user_stats` | 폐기 — 동일. 고유 사용자 수는 emotion_globe UID dedup 단일 소스 |
| 고유 사용자 수 산출 | emotion_globe 클라이언트 산출 + 저장 첫 기록 시 인메모리 증분 유지 |
| DB 컬렉션 수동 삭제 | 사용자 담당(§9.1). 에이전트는 코드 참조만 정리 |
| AI 피드백 영어 출력 | fallback 경로 한정 추정, 원인 판별 미완(§7.6) — 로그 재수집 전 수정 금지 |
| 지구구체 로그 도배 | 기존 이슈로 기록 + 저장 제안(미적용) |
| 로그 캡처 | adb logcat 필터 + 파일 저장 절차(§9.6) 사용, 콘솔 복붙 금지 |

---

## 부록 C. 2026-10-24 명령 이력 (시간순)

> 이 세션의 사용자 요청·승인·구현 기록이다. 충돌 시 가장 최신 지시가 우선한다(§0).

1. **[질문] 감정입력 Stage별 UI 화면 이름 정의 보고** — "감정입력 STAGE 단계별로 UI화면 이름을 정의내리며 보여줘"
   - 3단계 구조 코드 확인: 감정 선택(EmotionGridScreen, P04_EMOTION_SELECT) → 강도·멘션 입력(EmotionFlowScreen stage 1) → 통합 완료(stage 2). `totalStages = 2`, lastScreen `emotion_flow_stage_${n}` 확인.
2. **[IMPLEMENT] 감정입력 UX 3건** — (1) 감정 이름 앱 로케일 표시 (2) stage 1 키보드 표시 시 Skip/Confirm 숨김 현상 개선 (3) 다짐 설정 화면 키보드 선제시
   - 구현: `lib/utils/emotion_name_l10n.dart`(신규, `emotionJoy` 계열 기존 ARB 키 연결), emotion_grid_screen.dart(카드+설명 시트), emotion_flow_screen.dart(헤더 라벨 + viewInsets 오브 접힘), resolution_screen.dart(자동 포커스, 설정 모드 한정).
   - 검증: 전체 `flutter analyze` — 기존 경고 3건(§9.5)만, 신규 이슈 0. 실기기 표시 확인은 미수행.
3. **[질문/INVESTIGATE] Apple 로그인 구현 가능성** — "로그인 화면에서 APPLE로 로그인 기능을 구현할 수 있어? 아직 구현하지는 말고"
   - 조사: 코드상 이미 구현 완료(pubspec `sign_in_with_apple` ^6.1.0, nonce+sha256, `OAuthProvider('apple.com')`, Apple 재인증, l10n 7개). Android에서는 네이티브 API 미지원 — 웹 리다이렉트 브리지(HTTPS 도메인+Apple 등록+서버) 없이 불가. 선택지 A(버튼 숨김)/B(안내)/C(서버 브리지)/D(현상 유지) 보고.
4. **[IMPLEMENT 승인] Apple 로그인 버튼 숨김** — "ANDROID든 IOS에서든 APPLE에서 로그인 버튼은 숨김처리하자. 현재 단계에서는 안쓰여"
   - 구현: auth_screen.dart에서 UI 버튼 블록 + `_signInWithApple()` 핸들러 제거. 서비스/프로바이더 계층 유지(재활성화 대비). `flutter analyze` 스크린 파일 이슈 0.
   - 남은 것: 약관 문구 l10n(`byClickingSocialSignIn`)에 Apple 언급 잔존(7개 ARB 수정 대상), `signInWithApple` l10n 키 미사용 잔존.
5. **[ASSET/IMPLEMENT] Harness 문서 v5.1 갱신 + 커밋** — "하네스 프롬프트에 업데이트하고 깃허브 커밋도 해"
   - 이 문서 v5.1 작성(부록 C 신설). flutter_ui 리포에 본 세션 변경 파일 4개 커밋(리포 특성: 트래킹 파일이 소수인 부분 스냅샷 상태 — 커밋 시에도 대상 파일만 추가).

---

## 부록 D. 2026-11-03 명령 이력 (시간순)

> 이 세션의 사용자 요청·구현 기록이다. 충돌 시 가장 최신 지시가 우선한다(§0). 명령은 `C:\coding\opencode prompt_right now action.txt` 파일 전달 방식이었다.

1. **[IMPLEMENT] 통계 화면 + UNKNOWN AI 해석 (6항목 동시 요청)** — txt 파일: 통계 화면 진입(홈 '三' 메뉴→통계 버튼), 감정/다짐 탭 감정 기본, 감정 히트맵+원형 차트, 전환 경향 Top 3, 멘션 기반 Top 3, 다짐 통계는 에이전트 제안, '알수없음'+멘션 AI 해석(stage 3 피드백으로 출력)
   - 구현: `lib/services/stats_service.dart`(신규), `lib/screens/stats_screen.dart`(신규), `lib/screens/home_screen.dart`(메뉴 타일 추가), `lib/services/ai_service.dart`(`mention` 파라미터 + UNKNOWN 멘션 분류 지시), `lib/screens/emotion_flow_screen.dart`(호출부 2곳에 mention 전달), ARB 7개 언어 키 대량 추가 + `flutter gen-l10n`.
   - 멘션 패턴·다짐 키워드는 AI가 아닌 결정적 클라이언트 집계로 구현 — AI 호출 방식은 미채택(§7.5 승인 정책). 보고서에 대안 제시됨.
   - 검증: `flutter analyze` 신규 이슈 0, `dart format` 완료. 실기기 미검증.
2. **[IMPLEMENT] 다짐 키워드 Top 3** — "다짐 통계에는 가장 많이 한 다짐 키워드 top3를 뽑아줘"
   - 구현: `topIntentionKeywords()`(기록당 토큰 dedup) + 다짐 탭 `_IntentionKeywordRow` 섹션, ARB `statsIntentionKeywords` 7개 언어.
   - 검증: analyze 통과. **정정 후보 보고**: 섹션 기간 배지가 90일 표기인데 데이터는 최근 30일 로딩분 — (a)배지 제거 (b)30일 표기 (c)로딩 확장 중 선택 보고, 기본값은 배지 제거로 제안.
3. **[IMPLEMENT] 다짐 통계 그래프 강화** — "다짐 통계에서 그래프를 적극 보여줘봐"
   - 구현: 완료 도트를 두 차트로 교체 — `_IntentionTrendChart`(30일 바: 높이=일별 다짐 수, 색=완료/미완료, 5일 간격 날짜 라벨, 범례) + `_ReflectionLineChart`(0–10 복기 라인/점, 0·5·10 눈금, 복기 없으면 안내). `groupResolutionsByDate()` 추가. ARB `statsIntentionTrend`/`statsReflectionTrend`/`statsCompleted`/`statsNotCompleted`/`statsDay`/`statsNoReflection` 7개 언어.
   - 검증: analyze 통과, format 완료. 실기기 렌더링(바 폭·라벨 겹침) 미검증.
4. **[수습] 다짐 키워드 배지 제거** — 2항의 정정 후보에서 기본 정안(배지 제거) 적용. 사용자 선택은 아직 없음.
5. **[ASSET] Harness 문서 v5.2 갱신** — "하네스 프롬프트에 업데이트해둬"
   - 이 문서 v5.2 작성(부록 D 신설, §5 페이지 명칭에 P30_STATS, §7.2에 mention 계약 기록). 코드 변경 없음.

### 세션 확정 사항 요약 (v5.2 기준)

| 항목 | 결정 |
|---|---|
| 통계 화면 | 감정(기본)/다짐 2탭. 진입은 홈 메뉴 '통계' 타일 |
| 통계 산출 방식 | 클라이언트 결정적 집계 단일 경로, AI 호출·신규 컬렉션 없음 |
| 전환 경향 Top 3 | 동일 감정 연속 기록은 전환이 아님으로 제외 |
| 멘션 패턴 Top 3 | 키워드 토큰×감정 공동발생, 최빈 감정 과반 + 총 2회 이상만 채택 |
| 다짐 키워드 Top 3 | 다짐 내용 토큰, 기록당 dedup |
| UNKNOWN 멘션 | 멘션을 분류 근거로만 프롬프트 포함(§7.5 예외), 출력은 원문 인용 금지 |
| 기간 배지 불일치(다짐 키워드) | 배지 제거로 수습 — 90일 전체 집계 원하면 별도 요청 |
| 실기기 검증 | 전 항목 미수행 — §9.4 절차로 확인 필요 |

---

## 부록 E. 2026-11-05 명령 이력 (시간순)

> 이 세션의 사용자 요청·구현 기록이다. 충돌 시 가장 최신 지시가 우선한다(§0). 명령은 대화 직접 전달 방식이었다.

1. **[IMPLEMENT] 통계 감정 탭 보강 + 다짐 복기 추이 제거** — "통계-감정 탭에서 원형 그래프에서 감정 7가지 모두 범례를 보여줘. 가장 강했던 감정 top3도 보여줘. 감정별 평균 강도도 보여줘. 통계-다짐에서는 저녁 복기 추이는 빼."
   - 구현: `stats_service.dart`에 `emotionIntensityStats()` + `EmotionIntensityStat` 신설, `stats_screen.dart`에 "가장 강했던 감정 Top 3"·"감정별 평균 강도" 2섹션 추가 + 도넛 범례 `take(6)` 제거 + 다짐 탭 복기 라인 차트 섹션·위젯 2개 제거, ARB `statsIntensityTop`·`statsAvgIntensity` 신설 및 `statsReflectionTrend`·`statsNoReflection` 삭제(7개 언어), `flutter gen-l10n` 재생성.
   - 검증: `flutter analyze` 신규 이슈 0(기존 3건만), `dart format` 완료. 실기기 미검증 — 범례 7행 시 세로 공간 확인 권장.
2. **[IMPLEMENT] 통계 AI 해석 + 알림 리마인더 개편** — "통계 다짐을 좀 더 가치있고 흥미롭게. 자주 들어와 볼 수 있게. 통계 다짐 최상단에 AI가 분석하는 사용자의 다짐에 대한 의견, 통계 감정 탭 최상단에 AI가 분석하는 사용자의 감정에 대한 의견. 알림 리마인더가 켜져있으면 08시 12시 19시에 감정입력을 권장하는 알람. 기본값은 켜져있고 어플에서 끌 수 있어야."
   - 구현: `ai_service.dart` `generateStatsInsight()`(데이터 해시 캐시 + 진행 중 dedup + 대기형 throttle, digest 프롬프트), `stats_service.dart` `buildEmotionDigest()`/`buildIntentionDigest()`/`currentStreakDays()`(화면 내 `_currentResolutionStreak` 이전), `stats_screen.dart` 탭 최상단 `_InsightCard`(기존 `TypingText` 재사용), `notification_service.dart` 시각 08/12/19 + `isReminderEnabled()`/`setRemindersEnabled()` + `scheduleAllReminders()` 게이트 + `_cancelReminders()`(id 0..2 전용), `home_screen.dart` `_NotificationPermissionTile` → `_ReminderToggleTile` 스위치 개편, ARB `statsInsightCaption`·`notificationDeniedHint` 7개 언어.
   - 사용자 명시에 따라 v5.2의 "통계 AI 호출 없음" 원칙을 대체했다(최신 지시 우선).
   - 검증: `flutter analyze` 신규 이슈 0, `dart format` 완료. 실기기 미검증 — (a) 08/12/19 알림 수신 (b) 토글 OFF→ON 재예약 (c) 인사이트 카드 도착·캐시 재사용(logcat `[AiService]` 재호출 부재) 확인 필요.
3. **[ASSET] Harness 문서 v5.3 갱신** — "하네스 문서갱신해"
   - 이 문서 v5.3 작성(변경 이력 v5.3 섹션, §5 페이지 명칭 P30_STATS 갱신, §7.2 인사이트 계약 기록, §8.1 리마인더 절차 신설, 부록 E 신설). 코드 변경 없음.

### 세션 확정 사항 요약 (v5.3 기준)

| 항목 | 결정 |
|---|---|
| 통계 감정 탭 범례 | 도넛 범례 개수 컷 제거 — 기록된 감정은 전부 표시(7종 기준) |
| 감정 강도 지표 | 최대 강도 Top 3(최대 강도 기준, 동률은 평균) + 평균 강도(평균 기준) 신설 |
| 다짐 복기 추이 | 라인 차트 제거 — 지표 카드 "평균 복기"만 유지 |
| 통계 AI 해석 | 감정/다짐 탭 최상단 카드. digest(집계 수치·키워드 토큰)만 근거, 원문·다짐 내용 전문 미포함. 하루+동일 데이터 캐시. 사용자 명시로 v5.2 "AI 호출 없음" 원칙 대체 |
| 알림 리마인더 | 08/12/19 3회 감정입력 권장. 기본값 켜짐. 홈 메뉴 스위치로 ON/OFF. OFF 시 리마인더(id 0..2)만 취소, FCM·상시 알림 보존 |
| 통계 throttle | 통계 인사이트 경로는 fallback 즉시 반환 대신 남은 시간 대기(Emotion Flow 경로 §7.6은 미변경) |
| 실기기 검증 | 전 항목 미수행 — §9.4 절차로 확인 필요(알림 수신·인사이트 카드 포함) |

---

## 부록 F. 2026-11-06 명령 이력 (시간순)

> 이 세션의 사용자 요청·구현 기록이다. 충돌 시 가장 최신 지시가 우선한다(§0). 명령은 `C:\coding\opencode prompt_right now action.txt` 파일 전달 방식이었다.

1. **[IMPLEMENT] 감정분포 원형 그래프 여러 개 + Top 3 peak 표기 + 다짐 패턴 + 알림 점검 (통합 요청)** — txt 파일: 감정분포 원형 그래프 여러 개(①각 감정 분포 ②생존 위협 대응(슬픔/분노/두려움) vs 해소(기쁨) ③생존 위협(슬픔/분노/두려움/기쁨) vs 관계(사랑/싫음)), 가장 강했던 감정 Top 3에 각각 언제·멘션 표시, 다짐 패턴은 다짐의 나무 참조, 08/12/19 팝업 미표시 점검
   - 구현: `stats_service.dart`(EmotionIntensityStat peak 확장 + 그룹 산출 4종 함수), `stats_screen.dart`(`_GroupDonutChart`/`_SegmentsDonutPainter`/`_IntensityPeakRow`/`_IntentionRingPattern`, 감정 탭 그룹 도넛 2종, Top 3 peak 행, 다짐 탭 나이테 섹션), `lib/utils/resolution_achievement_color.dart`(신규 공용 util — 다짐의 나무 화면 연결), ARB 7개 언어 키 7종 신설 + `flutter gen-l10n`.
   - unknown의 그룹 소속 없음 → 그룹 도넛 ②③에서 제외(개별 도넛은 유지).
   - 검증: `flutter analyze` 신규 이슈 0(기존 3건만), `dart format` 완료. 실기기 미검증.
2. **[INVESTIGATE] 08/12/19 팝업 알람 미표시 원인 점검** — "매일 8시,12시,19시에 팝업 알람이 안뜨더라. 다시 점검해봐"
   - 원인 후보 보고: ①스마트 스킵 3시간 창(최우선 — 감정/다짐 저장 후마다 전체 리스케줄되며 해당 창 내 기록이면 회차 소멸) ②cancelAll()이 상시 알림(id 9999)까지 지우는 v5.3 회귀 ③inexact alarm의 Doze/OEM 지연 ④timezone 미설정은 **발화 시각 오차 없음**으로 배제(TZDateTime.from epoch 보존, timezone 0.10.1 소스 확인).
   - 확증 절차 제시: §9.6 캡처에서 `[Notification] Scheduled/Skipping reminder` 라인 확인.
3. **[IMPLEMENT 승인] 알림 원인 ① 수정(45분 창 축소)** — "1번해봐"
   - `_skipThreshold` 3시간 → 45분, 관련 주석·로그 문구 갱신(기존 주석의 "4시간"은 코드와 불일치한 오류였음).
   - 검증: analyze 신규 이슈 0.
4. **[IMPLEMENT 승인] 알림 원인 ②③ 수정** — "모두 승인할게"
   - ② `scheduleAllReminders()`의 `cancelAll()` → `_cancelReminders()`(id 0..2 전용). ③ `exactAllowWhileIdle` + `canScheduleExactNotifications()` 폴백, 매니페스트 `SCHEDULE_EXACT_ALARM` 선언, 스케줄 로그에 `exact: true/false` 추가.
   - 검증: analyze 신규 이슈 0. 실기기 미검증.
5. **[ASSET] Harness 문서 v5.4 갱신** — "하네스 문서 업데이트해"
   - 이 문서 v5.4 작성(변경 이력 v5.4 섹션, §5 감정/다짐 탭 구성 갱신, §8.1 리마인더 계약 갱신 — 45분 창·exact 폴백·id 전용 취소, 부록 F 신설). 코드 변경 없음.

### 세션 확정 사항 요약 (v5.4 기준)

| 항목 | 결정 |
|---|---|
| 그룹 도넛 ②③ | 대응(보라 `#6C5CE7`)/해소(노랑 `#FFD93D`), 위협(빨강 `#FF4757`)/관계(핑크 `#FF6B9D`). unknown은 그룹 제외 |
| Top 3 peak | 최대 강도 기록 중 최신 1건의 일자 + 멘션 원문. 멘션 없으면 일자만 |
| 다짐 패턴 | 다짐의 나무 나이테 미니 재현, 링 색 = `resolutionAchievementColor()` 공용 util |
| 스마트 스킵 | 45분 창(직전 기록만 그 회차 자제) |
| 상시 알림 | 예약 갱신 시 `cancelAll` 금지 — id 0..2 전용 취소 |
| exact alarm | `SCHEDULE_EXACT_ALARM` + 확인 후 폴백. OS에서 exact 해지 시 자동 inexact |
| timezone | 미설정이어도 발화 시각 유지(epoch 보존) — DST 지역 재사용 시 재검토 |
| 실기기 검증 | 전 항목 미수행 — §9.4 절차로 확인 필요(알림 수신·그룹 도넛 렌더링·나이테) |

---

## 부록 G. 2026-11-07 명령 이력 (시간순)

> 이 세션의 사용자 요청·구현 기록이다. 충돌 시 가장 최신 지시가 우선한다(§0). 명령은 `C:\coding\opencode prompt_right now action.txt` 파일 전달 방식이었다.

1. **[IMPLEMENT] 감정 기준선 + 누적 영역형 + 강도 변화 그래프 (3항목 통합 요청)** — txt 파일: ①감정 통계 baseline mood 지표(한국어명 "감정 기준선"). 목표는 꾸준히 입력한 감정 데이터로 기준선을 아는 것. 향후 "어떻게 하면 행복한지" 분석으로 확장 예정 — 하네스 프롬프트에 반드시 반영 ②감정분포/대응·해소/위협·관계를 누적 영역형으로 만들어 시간 흐름 비중 변화 표시, 원형 그래프 아래 배치 ③감정별 평균 강도에서 감정 탭 시 강도 변화 그래프 표시
   - 구현: `stats_service.dart`(`emotionBaselineStats()` + `EmotionBaselineStat`, `seriesShareOverTime()` + `SeriesShareSlice`, digest에 baseline 1행 추가), `stats_screen.dart`(`_BaselineCard`, `_StackedAreaChart`/`_StackedAreaPainter` 3카드, `_IntensityExpandableRow`(구 `_IntensityRow` 교체)/`_IntensityTrendChart`/`_IntensityLinePainter`), ARB 8키 × 7언어 + `flutter gen-l10n`.
   - 검증: `flutter analyze` 신규 이슈 0(기존 3건만), `dart format` 완료. 실기기 미검증.
2. **[ASSET] Harness 문서 v5.5 갱신** — "하네스 프롬프트에 반드시 업데이트해둬"(명령 항목 ① 포함 지시)
   - 이 문서 v5.5 작성(변경 이력 v5.5 섹션, §5.1 통계 감정 탭 구성 신설, P30_STATS 명칭 갱신, 부록 G 신설). 코드 변경 없음.

### 세션 확정 사항 요약 (v5.5 기준)

| 항목 | 결정 |
|---|---|
| 감정 기준선 | 강도 가중 valence(circumplexX) 0–100 지수. 전체 90일 vs 최근 14일 비교 마커 포함 |
| 기준선 확장 | "어떻게 하면 행복한지" 분석은 미정의 — 사용자 후속 명령 대기 |
| 누적 영역형 | 90일 = 13구간(주 단위), 비중 합 1, 무기록 구간 캐리포워드. 도넛 아래 배치 |
| 영역형 시리즈 | ①감정타입별(기록 있는 것, 개수 순) ②대응/해소 ③위협/관계. unknown은 시리즈 미포함 시 분모 제외 |
| 강도 변화 그래프 | 평균 강도 행 탭 → 펼침. 점 = 기록 1건, x = 실제 날짜 비율, y = 1–10 |
| l10n | 신규 키 8종 7개 언어. fr ICU 단일 인용부호 이스케이프 필요 |
| 실기기 검증 | 전 항목 미수행 — §9.4 절차로 확인 필요(기준선 카드·영역형 렌더링·행 펼침 포함) |

---

## 부록 H. 2026-11-07 2차 명령 이력 (시간순)

> 이 세션의 사용자 요청·구현 기록이다. 충돌 시 가장 최신 지시가 우선한다(§0). 명령은 `C:\coding\opencode prompt_right now action.txt` 파일 전달 방식이었다.

1. **[IMPLEMENT] 감정 기준선 재정의 + 강도 그래프 연속화 + 라벨 변경 (4항목 통합 요청)** — txt 파일: ①감정 기준선 정의 확정 — "감정들 중 어떤 감정이 가장 우세한지 보여주는 것, 일단은 빈도로 정하자" (v5.5의 valence 0–100 지수 방식 대체) ②누적 영역형 3종 — v5.5에서 이미 구현됨, 변경 없음 ③강도 변화 그래프에서 점 표기 제거, 연속적으로 표시 ④'생존 위협과 관계' → '외부환경 대응과 관계' 라벨 변경, 외부환경 대응 멤버(분노/기쁨/두려움/슬픔) 유지
   - 구현: `stats_service.dart`(`emotionBaselineStats` 재작성 — dominant/share/recentDominant/recentShare, `EmotionBaselineStat` 필드 교체, digest baseline 행 교체, threatGroup/threatRelationStats 주석 갱신), `stats_screen.dart`(`_BaselineCard` 전면 교체 — 우세 감정+비중+최근 14일 비교행, `_IntensityLinePainter` 점 제거→중점 2차 베지어 연속 곡선, 기록 1건은 짧은 수평 세그먼트, 주석 갱신), ARB 3키(statsThreat/statsThreatRelation/statsThreatRelationTrend) 값 교체 7개 언어 + `flutter gen-l10n`.
   - 키 이름은 유지하고 값만 교체했다(코드 참조 안정성). 대응(슬픔/분노/두려움)·해소(기쁨) 도넛 ②는 변경 대상 아님.
   - 검증: `flutter analyze` 신규 이슈 0(기존 3건만), `dart format` 완료. 실기기 미검증.
2. **[ASSET] Harness 문서 v5.6 갱신** — 세션 운영 관례에 따라 기준선 정의 변경을 문서에 반영
   - 이 문서 v5.6 작성(변경 이력 v5.6 섹션, §5.1 갱신 — 기준선 정의·외부환경 대응 라벨, P30_STATS 명칭 갱신, 부록 H 신설). 코드 추가 변경 없음.

### 세션 확정 사항 요약 (v5.6 기준)

| 항목 | 결정 |
|---|---|
| 감정 기준선 | **빈도 기준 우세 감정** (레코드 수 1위 + 비중). v5.5 valence 지수 방식 폐기 |
| 기준선 카드 | 우세 감정명(감정 색) + 비중% + 기록일 수 + 최근 14일 우세 감정 비교행. 게이지·증감 표기 제거 |
| 기준선 확장 | "어떻게 하면 행복한지" 분석은 여전히 미정의 — 사용자 후속 명령 대기 |
| 강도 변화 그래프 | 점 표기 제거, 연속 스무딩 곡선. 기록 1건은 수평 세그먼트로 표시 |
| 외부환경 대응 | 구 "생존 위협" 그룹(슬픔/분노/두려움/기쁨) 라벨만 변경, 멤버·색상 유지. ARB 키명 유지 |
| 대응·해소 도넛 ② | 변경 없음 (생존 위협 대응과 해소 표기 유지) |
| 실기기 검증 | 전 항목 미수행 — §9.4 절차로 확인 필요(기준선 카드·연속 곡선 렌더링 포함) |

---

## 부록 I. 2026-11-12 명령 이력 (시간순)

> 이 세션의 사용자 요청·구현 기록이다. 충돌 시 가장 최신 지시가 우선한다(§0). 명령은 대화 직접 전달 방식이었다. 이 세션에서 v5.7(1차)·v5.8(2차) 두 차례 구현이 이루어졌다.

1. **[PROPOSE_ONLY → IMPLEMENT 승인] 사용성 개선 6건 (v5.7)** — 사용자 요청으로 사용성 개선 후보를 제안 → 사용자가 A1/B1/B2/C1/C2/D2 6건 선택 → 구현.
   - 구현: A1(`ai_service.dart` throttle을 "남은 시간 대기 후 호출"로 통일 + `typing_text.dart` `startDelayMs` 신설 + emotion_flow 완료 화면 스태거 0/800/1600ms) / B1(stats 기간 선택기 `_PeriodSelector` 7/30/90일, 기본 90 — 도넛·영역형·Top3·평균강도·전환·멘션 적용, 히트맵·기준선 90일 고정) / B2(히트맵 셀 탭 → 일자 기록 bottom sheet, `_hitTestCell`·`_showDayDetail`) / C1(리마인더 시각 개별 편집 — prefs `reminder_time_$i`, `getReminderTimes`/`setReminderTime`, home `_ReminderTimeChip`) / C2(주간 요약 — 채널 `terrapado_weekly`, id 10, `weekly_summary_enabled` 기본 켜짐, `scheduleWeeklySummary`, 일요일 19:00) / D2(홈 위젯 — 신규 `widget_service.dart` + 네이티브 `WidgetHelper.kt`/`TerrapadoWidgetProvider.kt`/layout·drawable·xml·strings + Manifest receiver, MethodChannel `com.terrapado/widget`, MainActivity `WIDGET_CHANNEL`, emotion_provider 훅 4블록)
   - ARB 5키(`statsPeriod7`/`statsNoRecordsDay`/`reminderTimesEditable`/`weeklySummary`/`weeklySummaryCaption`) 7개 언어.
   - 검증: `flutter gen-l10n` + `dart format` + `flutter analyze` — 구현 중 `EmotionType` import 누락 에러 2건 발생 → 수정 후 기존 3건(§9.5)만. 실기기 미검증(릴리즈 빌드 + `adb install`은 사용자 담당).
2. **[PROPOSE_ONLY → IMPLEMENT 승인] UI 개선 4건 (v5.8)** — UI 개선 후보 제안 → 사용자가 A2/B2/B3/D1 선택 → 구현.
   - 구현: A2(완료 화면 대기 단축 — `_readyDelayFast` 800ms, AI 사전 준비 시) / B2(기간 선택기를 "감정 분포" 헤더 우측 `_SectionHeader.trailing`로 이동) / B3(히트맵 오늘 셀 흰 외곽선 — `_HeatmapCellPainter`, alpha 0.65·1.2px) / D1(My Strata 감정 타입 필터 칩 `_StrataFilterChips` — 클라이언트 필터 + `_ensureFilteredContent` 자동 이어받기 + `_expandedId` 확장 유지, 지층 시각화 무필터)
   - ARB 2키(`strataFilterAll`/`strataNoRecordsType`) 7개 언어.
   - 검증: 중간 RRect·import 에러 수정 후 기존 3건만. `dart format` 완료. 실기기 미검증.
3. **[PROPOSE_ONLY] 경량화 제안 (기능 유지 전제, 미적용)** — "기능은 그대로" 조건으로 제안 후 사용자 승인 없음: P1(R8 minify+shrinkResources — release 빌드타입 미설정, build.gradle.kts:49-53) / P2(미사용 의존성 cupertino_icons·flutter_svg·path_provider — import 0건 확인) / P3(`resourceConfigurations` 7 locale) / P4(`--split-per-abi`) / P5(폰트 감사 — Roboto 2블록 중복 선언) / R2(debugPrint 143건 무가드) / R3(stats 비지연 `ListView(children:)` 2곳) / R4(텍스처 로딩 정리) / S1(.env.json API 키 번들 보안 참고) / S2(데드 파일).
4. **[IMPLEMENT 승인] R1 적용 (v5.8)** — `_EmotionGlobeState`에 `WidgetsBindingObserver`: `paused`/`inactive`/`detached`에서 무한 repeat(30s/3s 컨트롤러) 정지, `resumed` 복귀 시 각도 이어 재개(`_syncAnimationRunning`). 라우트 가림은 프레임워크 TickerMode가 이미 처리(변경 없음). 검증: analyze 기존 3건만.
5. **[ASSET] Harness 문서 v5.8 갱신** — "오늘 작업명령을 하네스 프롬프트에 업데이트해둬"
   - 이 문서 v5.8 작성(변경 이력 v5.7·v5.8 섹션, §5.1 갱신 — 히트맵 탭 상세·오늘 외곽선·기간 선택기, §5 My Strata 필터 신설, P20/P30 명칭 갱신, §7.1 진입 게이트·스태거, §7.2 throttle 통일, §7.6 후보 1 적용 완료 표기, §8.1 시각 개별 편집, §8.2 주간 요약·§8.3 홈 위젯 신설(구 §8.2 검증 시나리오는 §8.4로 재배치), 부록 I 신설). 코드 변경 없음.

### 세션 확정 사항 요약 (v5.8 기준)

| 항목 | 결정 |
|---|---|
| AI throttle | Emotion Flow·통계 경로 모두 "남은 시간 대기 후 호출" 통일(§7.6 후보 1 적용) |
| 완료 화면 | AI 3문장 스태거(0/800/1600ms) + AI 사전 준비 시 진입 대기 2초→800ms |
| 통계 기간 선택기 | 7/30/90일, "감정 분포" 헤더 우측. 도넛/영역형/Top3/평균강도/전환/멘션 적용, 히트맵·기준선 90일 고정 |
| 히트맵 | 셀 탭 → 일자 기록 bottom sheet + 오늘 셀 흰 외곽선 |
| 리마인더 | 시각 3종 개별 편집(기본 08:00/12:00/19:00), 저장 즉시 재예약 |
| 주간 요약 | 일요일 19:00, 최빈 감정(건수), 토글 기본 켜짐, id 10 — 메시지는 예약 시점 스냅샷 |
| 홈 위젯 | 네이티브 AppWidget + MethodChannel(`com.terrapado/widget`). 탭 → 감정 선택 진입(기존 Log 흐름 재사용) |
| My Strata 필터 | 클라이언트 필터 + 자동 이어받기(`hasMore` 소진 시 정지), 확장 id 기반 유지 |
| 지구구체 애니메이션 | 백그라운드/화면 꺼짐 repeat 정지, resumed 복귀 시 재개. 라우트 가림은 TickerMode 처리 |
| 경량화 | R1만 적용. P1–P5/R2–R4/S1–S2는 PROPOSE_ONLY(미적용) |
| 실기기 검증 | 전 항목 미수행 — 릴리즈 빌드 + `adb install`은 사용자 담당(§9.1) |

---

## 부록 J. 2026-11-13 명령 이력 (시간순)

> 이 세션의 사용자 요청·구현 기록이다. 충돌 시 가장 최신 지시가 우선한다(§0). 명령은 대화 직접 전달 방식이었다. 이 세션에서 3차 구현(Nocturne / Night Glass / 1차 묶음)이 이루어졌다.

1. **[ASSET/INVESTIGATE] Harness 문서 v5.8 파악** — "C:\coding\harness\Terrapado_Harness_Prompt_v5.md 파악해"
   - 문서 구조·운영 원칙·현재 상태(v5.8 기준) 요약 보고.
2. **[PROPOSE_ONLY] 디자인 개선 제안 8건 (D1–D8)** — "디자인 개선 제안해봐."
   - 실제 코드 정독 기반 제안: D1(테마 누락 화면 — stats 고정색 `rgba(30,30,40)`) / D2(공용 카드 위젯) / D3(반경·색 토큰화) / D4(액센트 패밀리 정리) / D5(타이포 스케일) / D6(저대비 접근성) / D7(통계 밀도·휴식) / D8(홈 여백 구조화). **전부 미적용**.
3. **[PROPOSE_ONLY → IMPLEMENT 승인] Nocturne 8건 중 4건 + 지층 고정** — "뭔가 좀 안이쁜데, 개선할 방법없냐고" → 제안 후 "n1,n3,n6,n7 적용해. 그리고 나의 지층 메뉴에서 감정레이어 누적 지층이 화면에서 차지하는 비중은 처음 나의 지층 메뉴를 실행했을때의 넓이에서 커지지마."
   - 구현: N1(`AmbientBackground`/`_StarfieldPainter` — 고정 시드, 정적, paint 1회 캐시) / N3(홈 CTA 오브 — 오늘 감정색 그라디언트+글로우) / N6(도넛 헤일로·갭, 히트맵 셀 하이라이트, 영역형 밴드 페이드 — `Gradient.linear`→`createShader()` 3곳) / N7(백버튼 톤 통일 — stats·My Strata) / 지층 고정(`_strataHeight` 첫 페이지 1회 산정 + painter 레이어 압축).
   - 검증: analyze 기존 3건만, `dart format` 완료. 실기기 미검증.
4. **[PROPOSE_ONLY] Night Glass 제안 12건** — "아이폰 어플 처럼 디자인과 ui/ux를 개선하는 것을 검토중이야. 제안해봐.아직 반영하지는 마."
   - A(컨트롤 교체: A1 스위치/A2 휠 피커/A3 세그먼트) / B(모션: B1 프레스/B2 트랜지션/B3 스태거/B4 시트 등장) / C(질감: C1 frosted 헤더/C2 유리 시트/C3 깊이 카드) / D(UX 디테일: D1 Large Title/D2 햅틱/D3 캡슐 토스트/D4 탄성 스크롤).
   - 우선순위 권고: 1차 묶음(A1+A2+A3+B1) / 2차 묶음(B3+B4+D4) / 3차 묶음(C1+C2+C3+D1~D3).
5. **[PROPOSE_ONLY → IMPLEMENT 승인] Night Glass 7건 + 나무 1년치** — "B2,B3,B4,C1,C2,D2,D4 적용해.다짐의 나무 메뉴는 스크롤 내려야 나이테가 쌓이는데, 처음부터 쌓였으면 해. 1년치를 보여주면 좋겠어."
   - 구현: B2(전역 `CupertinoPageTransitionsBuilder` — **Flutter 3.44에서 material→cupertino 라이브러리 이동 확인, `show` import로 해결** + grid→flow 슬라이드·패럴랙스) / 신규 위젯 3종(`glass_sheet.dart`·`stagger_in.dart`·`frosted_header.dart`) / B4(홈 `AnimatedScale` 0.94+시트 탄성 스크롤) / C1(stats·My Strata frosted 헤더 — My Strata는 `CustomScrollView` 리팩터) / C2(홈 메뉴·감정 설명 유리 시트) / B3(stats 섹션·My Strata 타일 스태거) / D2(토글·탭 전환·시트 햅틱) / D4(`BouncingScrollPhysics` 6곳) / **나무 1년치**(`_treeResolutions` — limit 365 별도 쿼리 1회, 링 폭 `min(1.0, (maxRadius-5)/n)` 압축, N7 백버튼 포함).
   - 검증: 중간 에러(cupertino import 누락, today_log 위젯 import 누락) 수정 후 analyze 기존 3건만, format 완료.
6. **[INVESTIGATE] 1차 묶음 의미 질의** — "이제 1차묶음이라는 것을 좀더 쉽게 설명해봐.뭔지 몰라서 적용을 못했어. 항목 하나씩 나눠서 직관적으로 설명해."
   - "1차 묶음" = Night Glass 제안의 우선순위 1세트 **A1(iOS 스위치)+A2(휠 피커)+A3(세그먼트)+B1(프레스 피드백)**임을 항목별 직관 설명으로 보고. 구현 없음.
7. **[PROPOSE_ONLY → IMPLEMENT 승인] 1차 묶음 적용 + 원상복구 장치** — "1차 묶음 적용해. 혹시 모르니까 적용 후 원상복구할 수 있게 지금 작업하는 건 구별을 지어줘."
   - 구현: A1(`CupertinoSwitch` 2곳 — `activeTrackColor` 사용, deprecated `activeColor` 회피) / A2(`_WheelTimePicker` — 유리 시트 내 시/분 휠, `TimeOfDay` 파이프라인 유지) / A3(`_SegmentControl` — 유리 트랙+슬라이딩 썸, `_tabController` 유지) / B1(`pressable_card.dart` 신설 — **`Listener` 기반 피드백으로 제스처 아레나 비참여**, 외부 탭 처리 충돌 방지; 홈 메뉴 4행·My Strata 타일·통계 기간 칩/강도 행 적용).
   - **원상복구 장치**: 백업 `.backup_1cha_20261113/`(home/stats/today_log — 적용 직전 상태. git 리포는 대부분 untracked라 커밋 기반 롤백 대신 파일 백업 채택) + 마커 주석(`// A1(1차 묶음):` 등). 백업 폴더가 analyze에 스캔되는 문제 → **점 폴더로 리네임해 분석 제외**.
   - 검증: 중간 에러(백업 폴더 스캔, `CupertinoSwitch` show 누락) 수정 후 analyze 기존 3건만, format 완료.
8. **[ASSET] Harness 문서 v5.9 갱신** — "오늘 작업 프롬프트 업데이트해"
   - 이 문서 v5.9 작성(변경 이력 v5.9 섹션, §5 공용 위젯·재질 신설 + My Strata 지층 고정, P20/P21/P30 명칭 갱신, §8.1 휠 피커 표기, 부록 J 신설). 코드 변경 없음.

### 세션 확정 사항 요약 (v5.9 기준)

| 항목 | 결정 |
|---|---|
| 디자인 방향 | Nocturne(미스틱 깊이) + Night Glass(iOS 감각) 병행 — 기존 다크 톤 유지, 무한 애니메이션 추가 없음 |
| 공용 위젯 | `showGlassSheet`/`FrostedHeaderScaffold`/`StaggerIn`/`PressableCard` 4종 신설 — 신규 화면은 우선 재사용 |
| 전역 트랜지션 | android/iOS 모두 `CupertinoPageTransitionsBuilder` — Flutter 3.44부터 cupertino 라이브러리 소속(`show` import) |
| 스크롤 물성 | `BouncingScrollPhysics` 통일(6곳) |
| 프레스 피드백 | `Listener` 기반(제스처 아레나 비참여) — 기존 탭 핸들러와 중첩 안전. easeOutBack 스프링 복귀 |
| My Strata | frosted 헤더 + `CustomScrollView` 통합. 지층 높이 첫 페이지 기준 고정(초과 시 레이어 압축) |
| 다짐의 나무 | 나이테 1년치(최대 365링) 초기 로드 — Firestore 읽기 1회 추가 승인됨. 목록은 30건 페이지네이션 유지 |
| 1차 묶음 | A1 `CupertinoSwitch`(activeTrackColor) / A2 휠 피커 / A3 `_SegmentControl` / B1 `PressableCard` — 전부 적용 |
| 롤백 장치 | `.backup_1cha_20261113/` 백업 + 마커 주석. **백업은 1차 묶음 직전 시점 — 이전 라운드 변경분 포함 주의** |
| 미적용 제안 | 디자인 토큰화 D1–D8 / Nocturne N2·N4·N5·N8 / Night Glass C3·D1·D3 / 경량화 P1–P5·R2–R4·S1–S2 — 전부 PROPOSE_ONLY |
| 실기기 검증 | 전 항목 미수행 — 특히 frosted/유리(`BackdropFilter`) 저사양 프레임, 나무 365링 렌더, 지층 압축 시인성 확인 필요(§9.4) |


---

## 부록 K. 2026-11-14 명령 이력

1. 메인 프롬프트 v5.9 파악 — 문서 읽기, 구현 없음.
2. 리마인더 미수신 확인·조치 — 기기 권한/알람 등록 정상, 예약 수신기 없음 확증. Manifest 수신기·부팅 복원 추가, 스마트 스킵 다음 날 유지. 실기기 임시 예약 알림 발화 및 정리 확인.
3. iOS처럼 깔끔하고 직관적인 UI 검토 후 **U1/U2/U4/U5/U7 적용 승인**, 대응·해소 비중 변화의 해소 아래/대응 위 요청 — 하단 탐색·홈 비율·텍스트·묶음 목록·터치 영역 구현.
4. 기록의 지층 및 설명 삭제, 나무를 통계의 다짐 패턴 자리로 이동, 히트맵/나무 기간 90/180/1년 선택, 하단 메뉴 재탭 내부 탭 전환 — 구현 및 당시 회귀 테스트 14건 통과.
5. 휴대폰 디버그 실행 요청 — 최신 소스 디버그 빌드 설치·실행.
6. 감정 기준선은 가장 자주 느끼는 감정이라는 정의 재확인 + 모든 감정 강도의 평균·표준편차 추가 요청. **휴대폰 디버깅 후 프롬프트 갱신 및 GitHub 커밋** 지시 — 통계 필드/표시·7개 언어·회귀 검사 구현, 최종 테스트 18건 및 analyze 기존 2건 확인, 기기 기준선 카드 실제 표시 확인 후 이 문서 v5.10 갱신. 앱 소스 로컬 Git에는 원격 저장소가 없으며, GitHub 연결은 기존 `xenocurrency/terrapado-harness-prompt` 저장소를 사용한다.

# 헬스장 로봇 GUI

7인치 원형 디스플레이(1080×1080, LCD AA 177.55mm)용 인터랙티브 화면.
파일 하나(`index.html`)로 끝나고, 빌드도 의존성도 없다.

- 배포 : https://dab2n.github.io/baton-gym/
- 실기기(키오스크) : 위 주소 뒤에 `#screen` — 목업 하우징·토글·카메라 썸네일이 빠지고 스크린이 화면을 꽉 채운다
- 병원 로봇 : https://github.com/dab2n/baton — 디자인 시스템(스크린 비율·베젤·배경 레이어·모션·카메라 스펙)을 그대로 공유한다

## 조작

| 입력 | 동작 |
|---|---|
| 얼굴이 ≈30cm 안으로 | 구슬이 화면을 채우며 첫 문장 |
| 엄지척 | 다음 단계로 진행 (이후 자동 재생) |
| 화면 탭 / Space / Enter | 한 장면씩 수동 진행, 마지막에서 처음으로 |

카메라나 권한이 없으면 인식만 조용히 꺼지고 탭 조작이 남는다.

## 장면

피그마 `2436:392` (STAGE 01~05) 기준.

| # | 그래픽 | 화면 | 다음으로 |
|---|---|---|---|
| 1 | idle | 검정 + 중앙 구슬이 배회·발광 | 얼굴 근접 |
| 2 | fill | `Ring detected. / Ready to receive your workout context.` | 엄지척 |
| 3 | search | 베젤 안쪽이 숨쉬듯 일렁임 (컨텍스트 수신) | 4.2s |
| 4 | full | 기기 칩 등장 → `Welcome back, Somi.` | 4.3s |
| 5 | full | `Today's context` · 어깨/상체 | 6.0s |
| 6 | full | `Analyzing your workout context…` | 4.6s |
| 7 | full | `Shoulder-friendly mode recommended.` · Level 03 | 6.4s |
| 8 | full | `Adjusting resistance.` · Level 03 → 02 | 6.0s |
| 9 | full | `You're all set.` | 6.0s |
| 10 | full | `Ready? Let's begin your workout.` · 10 REPS | 5.2s |
| 11–14 | full | 카운트 `3/10` `4/10` `5/10` `10/10` + 코칭 | 4.2~5.6s |
| 15 | full | `Workout complete.` · 30 REPS · 3 SETS | 5.2s |
| 16 | full | `Today's workout` 요약 | 6.4s |
| 17 | full | `Workout context saved.` | 6.8s |
| 18 | full | `Goodbye, Somi.` | 유지 |

장면은 `index.html` 의 `SCENES` 배열 하나로만 관리한다. 문구·머무는 시간·칩 노출을
거기서 고치면 되고, CSS 는 건드릴 필요가 없다.

## 텍스트 컬러

엄지척 **이전**(장면 2)은 `mix-blend-mode:normal` — 투명감 없이 단색.
**이후**는 `color-burn` — 뒤의 파란 블롭이 흘러 지나갈 때마다 글자 색이 미묘하게 바뀐다.

## 인식 값 보정

웹캠 화각마다 다르므로 실기기에서 `index.html` 하단 세 값만 만진다.

```js
const NEAR = .34;   // 얼굴 폭 ÷ 프레임 폭. 화각 65° 기준 0.34 ≈ 30cm, 0.5 ≈ 20cm
const HITS = 3;     // 트리거까지 필요한 누적 프레임
const GMIN = .35;   // 엄지척으로 인정할 최소 점수
```

## 아직 안 넣은 것

- STAGE 04 `Manual Control` (사용자가 직접 저항/높이/위치를 조절하는 분기) — 리모컨 입력이 필요해서 선형 데모에서 뺐다
- 칩의 `K Fitness · Pull Station 02` 는 임시 이름

# Roomake — 빌드 노트

[English](README.md) · [简体中文](README.zh-CN.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Deutsch](README.de.md) · [Italiano](README.it.md) · [Español](README.es.md)

**Roomake** ([www.roomake.top](https://www.roomake.top))는 AI 인테리어 디자인 웹 앱입니다. 방 사진 한 장을 업로드하고 방 유형과 최대 네 가지 스타일(또는 직접 작성한 브리프)을 고르면 비포/애프터 렌더링을 돌려받습니다. 운영 주체는 RooMake Teams입니다.

이 저장소에는 제품의 소스 코드가 담겨 있지 **않습니다**. 대신 사이트가 어떻게 만들어졌는지 — 아키텍처, 다국어 구성, SEO 결정, 배포 규율 — 를 일곱 개 언어로 기록합니다. 이를 적어 둔 것은 우리 자신을 위해서이기도 하고, 소규모 팀이 어떻게 제대로 된 i18n과 제대로 된 SEO를 갖춘 크레딧 기반 AI 이미지 제품을 출시하는지라는 같은 질문에 계속 답해 왔기 때문이기도 합니다.

비슷한 것을 만들고 계신다면 — AI 이미지 생성, 크레딧, 결제, 다중 로캘 SEO — 이 노트가 몇 군데 막다른 길은 피하게 해 줄 것입니다. 여기 설명하는 모든 방식은 실제 프로덕션에서 운영 중인 것이며, 그 이유를 깨닫게 해 준 실수들까지 포함입니다.

## 문서 구성

| 문서 | 다루는 내용 |
|---|---|
| [01 · 개요](docs/ko/01-overview.md) | Roomake가 무엇인지, 제품 화면의 구성, 그리고 두 개의 사이트(.top / .cn) |
| [02 · 기술 스택](docs/ko/02-tech-stack.md) | Next.js 16, 렌더링 파이프라인, 크레딧과 결제, 데이터 레이어 |
| [03 · i18n 아키텍처](docs/ko/03-i18n.md) | 하나의 코드베이스 위의 여섯 로캘, hreflang 정책, CJK 폰트, 현지화된 법적 고지 페이지 |
| [04 · SEO 플레이북](docs/ko/04-seo.md) | 기술 SEO, 구조화된 데이터, `llms.txt`, 그리고 AI 크롤러 정책 |
| [05 · 배포](docs/ko/05-deployment.md) | Docker, 빌드 시점 스코프 플래그, 캐싱, 그리고 롤백 규율 |

모든 문서는 **English · 简体中文 · 日本語 · 한국어 · Deutsch · Italiano · Español**로 제공됩니다 — 각 파일 상단의 언어 줄에서 전환할 수 있습니다.

## 핵심 요약

| | |
|---|---|
| 제품 | AI 방 리디자인: 6가지 방 유형 × 34개 스타일 프리셋, 최대 4개 스타일 조합 또는 자유 텍스트 브리프 |
| 프런트엔드 | Next.js 16 (App Router), React 19, TypeScript, Tailwind CSS, 서버 컴포넌트 우선 |
| 렌더링 | 호스티드 프로바이더 API를 통해 서빙되는 Flux 계열 이미지 모델, 그 앞에 얇은 내부 게이트웨이 |
| 결제 | 선불 크레딧, 렌더링 1회당 크레딧 1개, 렌더링 실패 시 자동 환불, 해외 결제는 Creem(merchant of record) |
| 제품 로캘 | English + 简体中文 서비스 중 · 日本語 / Deutsch / Français / 한국어 다음 순서로 출시 진행 중 |
| 문서 언어 | EN · ZH · JA · KO · DE · IT · ES |
| 배포 | 리버스 프록시와 CDN 뒤의 Docker(standalone 빌드), 지역별로 분리된 두 개의 독립 배포 |

## 링크

- 제품: [www.roomake.top](https://www.roomake.top) · 중국 본토: [www.roomake.cn](https://www.roomake.cn)
- 이 노트에 대한 질문이나 정정 사항: `support@roomake.top`

## 라이선스

이 저장소의 텍스트는 [Creative Commons Attribution 4.0](LICENSE) 라이선스로 제공됩니다. 자유롭게 번역하고, 수정하고, 재사용할 수 있습니다 — 저작자 표시는 감사히 받겠지만, 라이선스 조건 이상으로 요구하지는 않습니다.

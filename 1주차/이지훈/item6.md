## 편집기를 사용한 타입 시스템 탐색

> TS는 JS를 변환하는 역할도 하지만, 가장 중요한 역할은 타입 시스템에 있다.

- 타입 시스템은 TSC(타입스크립트 컴파일러)를 통해서 TS를 JS로 컴파일 하는 작업이 주요 목적
- 또한 코드 자동완성, 검색, 리팩토링 등의 언어 서비스 또한 제공
- 편집기는 이 언어 서비스를 통해서 개발 단계에서 가이드를 제공

이건 뭐 편집기가 너무많다, vsc, vs, intelliJ 등 뭐.. 지원해주는 기능이 너무 다양함.<br>

- 자동 완성 (Autocompletion)
- 타입 툴팁 (Type Information)
- 타입 오류 강조 (Type Error Highlighting)
- 타입 정의 이동 (Go to Definition)
- 타입 추론 (Type Inference)
- 타입 정적 분석 (Static Type Analysis)
- 인라인 도움말 (Inline Documentation)

<hr>

## 요약

1. 편집기는 TS가 제공하는 언어 서비스를 통해서 개발 단계에서 가이드를 제공하고 있다.

2. 라이브러리의 형식이 선언된 `.d.ts` 파일을 통해서 해당 라이브러리의 기능을 명확히 파악할 수 있다.

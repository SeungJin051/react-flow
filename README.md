# React + TypeScript + Vite With React Flow

React Flow 라이브러리 학습을 목표로 합니다.

## Install

```bash
  pnpm install
```

## Run

```bash
  pnpm run dev
```

# React Flow 핵심 요약

## 1. React Flow와 TypeScript

TypeScript 환경에서 React Flow를 사용할 때 가장 흔히 발생하는 에러는 **타입 불일치**입니다.

- **문제**: useState로 노드를 관리하면 기본 Node 타입과 사용자가 정의한 data 타입 간의 충돌 발생.
- **해결**: `useNodesState<AppNode>(initialNodes)` 처럼 제네릭을 사용하여 타입을 명시.

커스텀 노드의 경우 NodeProps 타입을 활용해 data 내부 구조를 정의.

## 2. 핵심 커스텀 훅 (Hooks)

React Flow는 복잡한 로직을 직접 짤 필요 없게끔 강력한 훅을 제공합니다.

- `useNodesState`: 노드의 상태 관리 및 자동 업데이트, onNodesChange를 함께 반환하여 드래그 등을 자동 처리
- `useEdgesState`: 엣지의 상태 관리 및 자동 업데이트, 연결 상태 변화를 관리
- `useReactFlow`: 전역 API 호출, 줌 조절 노드 추가, 좌표 변환(screenToFlowPosition) 등 명령형 제어
- `useConnection`: 실시간 연결 상태 감지, 선을 끌어서 연결 중인 도중의 상태를 확인

## 3. 노드 성능 최적화 useCallback & React.memo

n8n 같은 대규모 워크플로우 툴에서는 수백 개의 노드가 렌더링되므로 성능 최적화가 필수입니다.

### 💡 왜 useCallback인가?

- **정의**: 함수(로직)를 메모리에 저장(캐싱)하여 리렌더링 시 **함수의 주소값**을 고정하는 훅.
- **이유**: 자바스크립트에서 함수는 객체이므로, 주소값이 변하면 자식 컴포넌트는 "Props가 바뀌었다"고 오해하여 리렌더링을 시도함.
- **시너지**: 자식 컴포넌트가 `React.memo`로 감싸져 있을 때, `useCallback`으로 고정된 함수를 넘겨줘야만 불필요한 리렌더링이 실제로 방지됨.
  - `useCallback` vs `useMemo` 요약
    - `useCallback`: **함수 로직**을 보관 $\rightarrow$ "신분증 유지"
    - `useMemo`: **실행 결과**를 보관 $\rightarrow$ "재계산 방지"

## 4. 커스텀 노드 제작 3단계

n8n 스타일의 노드를 만드는 표준 절차입니다.

1. **컴포넌트 작성**: `Handle`(연결점)을 포함한 UI 제작 및 `memo`로 감싸기.

2. **타입 매핑**: `const nodeTypes = { myType: MyComponent }` 형태로 등록 (컴포넌트 외부 선언 권장).

3. **타입 속성 적용**: 노드 데이터의 `type: 'myType'` 타입 속성 지정.

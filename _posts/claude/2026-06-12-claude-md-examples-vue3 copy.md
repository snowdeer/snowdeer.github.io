---
layout: post
title: CLAUDE.md 예제 (Vue3)
category: Claude
permalink: /common-sense/:year/:month/:day/:title/

tag: [claude]
---

# CLAUDE.md 예제 (Vue3)

````md
# CLAUDE.md

이 파일은 Claude가 이 프로젝트에서 코드를 생성하거나 수정할 때 참조하는 가이드입니다.

---

## 프로젝트 개요

- **프레임워크**: Vue 3 (Composition API)
- **언어**: TypeScript
- **빌드 도구**: Vite
- **상태 관리**: Pinia
- **라우터**: Vue Router 4
- **HTTP 클라이언트**: Axios (또는 ofetch)
- **구조**: 기능(feature)별 모듈 분리

---

## 프로젝트 구조

```
src/
├── main.ts                        # 앱 진입점
├── App.vue                        # 루트 컴포넌트
├── router/
│   └── index.ts                   # Vue Router 설정
├── stores/                        # Pinia 스토어 (기능별로 파일 분리)
│   ├── auth.ts                    # 예: 인증 스토어
│   ├── dashboard.ts               # 예: 대시보드 스토어
│   └── settings/
│       ├── wifi.ts                # 예: Wi-Fi 설정 스토어
│       └── audio.ts               # 예: 오디오 설정 스토어
├── network/
│   └── api/
│       ├── client.ts              # Axios 인스턴스 및 인터셉터
│       ├── dashboard.ts           # 대시보드 관련 API 함수
│       └── settings/
│           ├── wifi.ts            # Wi-Fi 설정 API 함수
│           └── audio.ts           # 오디오 설정 API 함수
├── views/                         # 라우트에 대응하는 페이지 컴포넌트
│   ├── HomeView.vue
│   ├── dashboard/
│   │   └── DashboardView.vue
│   └── settings/
│       ├── WifiSettingView.vue
│       └── AudioSettingView.vue
├── components/                    # 전역 공용 컴포넌트
│   └── BaseButton.vue
└── types/
    └── index.ts                   # 전역 공용 타입
```

### 구조 원칙

- **`views/`** — 라우트 1개에 View 컴포넌트 1개. 페이지 레이아웃만 담당하고, 비즈니스 로직은 스토어에 위임한다.
- **`stores/`** — 화면 구조와 동일한 디렉토리 계층으로 관리한다. 스토어가 `network/api/`를 직접 호출하며, View는 스토어만 바라본다.
- **`network/api/`** — 순수 HTTP 호출 함수만 둔다. 상태를 갖지 않으며, 스토어 외부에서 직접 호출하지 않는다.
- **`components/`** — 여러 View에서 공통으로 쓰는 UI 컴포넌트만 둔다. 특정 View 전용 컴포넌트는 `views/<domain>/components/`에 둔다.

---

## 주요 명령어

```bash
# 의존성 설치
npm install

# 개발 서버 실행
npm run dev

# 프로덕션 빌드
npm run build

# 타입 체크
npm run type-check

# 린트
npm run lint

# 프리뷰 (빌드 결과 확인)
npm run preview
```

---

## 코드 규칙

### 공통 원칙

- 모든 파일은 **TypeScript**로 작성한다. `.js`, `.jsx` 파일을 생성하지 않는다.
- `any` 타입을 사용하지 않는다. 불가피한 경우 `unknown`을 쓰고 타입 가드를 적용한다.
- `as` 타입 단언은 최소화한다. 가능하면 타입 가드 또는 제네릭으로 대체한다.
- 타입은 `interface`를 우선 사용하고, 유니온·교차 타입 등 표현식이 필요한 경우만 `type`을 사용한다.

---

### Vue 컴포넌트

- **Composition API + `<script setup>`** 방식만 사용한다. Options API는 쓰지 않는다.
- `defineProps`, `defineEmits`에는 반드시 TypeScript 제네릭 타입을 명시한다.
- Props는 가능하면 `withDefaults`로 기본값을 정의한다.
- 컴포넌트 파일명은 **PascalCase**로 작성한다. (예: `UserProfileCard.vue`)
- 한 컴포넌트 파일이 300줄을 넘으면 분리를 고려한다.
- 템플릿에서 복잡한 로직은 `computed`로 분리하거나 스토어에 위임한다.

```vue
<!-- 컴포넌트 예시 -->
<script setup lang="ts">
import { computed } from "vue";

interface Props {
  userId: number;
  isActive?: boolean;
}

const props = withDefaults(defineProps<Props>(), {
  isActive: false,
});

const emit = defineEmits<{
  select: [id: number];
  close: [];
}>();

const label = computed(() => (props.isActive ? "활성" : "비활성"));
</script>

<template>
  <div :class="{ active: isActive }" @click="emit('select', userId)">
    {{ label }}
  </div>
</template>
```

---

### Pinia 스토어

- 스토어는 **Setup Store** 방식(`defineStore` + `setup()` 형태)을 사용한다.
- 스토어 파일은 `src/stores/` 아래에 화면 구조와 동일한 경로로 분리한다. (예: `stores/settings/wifi.ts`)
- 스토어 ID는 파일 경로와 일치시킨다. (예: `'settings/wifi'`, `'auth'`)
- **스토어가 `network/api/`를 직접 호출하는 유일한 진입점이다.** View나 컴포넌트는 스토어만 바라본다.
- 스토어 내부에서 직접 `router.push()`를 호출하지 않는다. 라우팅은 View에서 처리한다.
- 로딩·에러 상태는 스토어 안에서 관리한다.

```ts
// src/stores/settings/audio.ts
import { ref } from "vue";
import { defineStore } from "pinia";
import {
  fetchAudioConfig,
  updateAudioConfig,
} from "@/network/api/settings/audio";
import type { AudioConfig } from "@/types";

export const useAudioStore = defineStore("settings/audio", () => {
  const config = ref<AudioConfig | null>(null);
  const isLoading = ref(false);
  const error = ref<string | null>(null);

  async function loadConfig() {
    isLoading.value = true;
    error.value = null;
    try {
      config.value = await fetchAudioConfig();
    } catch (e) {
      error.value = "오디오 설정을 불러오지 못했습니다.";
    } finally {
      isLoading.value = false;
    }
  }

  async function saveConfig(next: AudioConfig) {
    await updateAudioConfig(next);
    config.value = next;
  }

  return { config, isLoading, error, loadConfig, saveConfig };
});
```

---

### API 클라이언트 (`network/api/`)

**데이터 흐름**: `View` → `Store` → `network/api/` (단방향, 역방향 금지)

- Axios 인스턴스는 `src/network/api/client.ts` 한 곳에서만 생성한다.
- 인증 토큰 주입과 공통 에러 처리는 인터셉터에서 일괄 처리한다.
- `network/api/` 함수는 **순수 HTTP 호출만** 담당한다. 상태(`ref`, `reactive`)를 갖지 않는다.
- `network/api/` 함수는 반드시 스토어를 통해서만 호출한다. View나 컴포넌트에서 직접 호출하지 않는다.
- API 함수는 화면 구조와 동일한 경로로 분리한다. (예: `network/api/settings/wifi.ts`)
- 모든 API 함수의 반환 타입을 명시한다.

```ts
// src/network/api/client.ts
import axios from "axios";

export const apiClient = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL,
  timeout: 10_000,
});

apiClient.interceptors.request.use((config) => {
  const token = localStorage.getItem("token");
  if (token) config.headers.Authorization = `Bearer ${token}`;
  return config;
});

apiClient.interceptors.response.use(
  (res) => res,
  (err) => {
    // 공통 에러 처리 (401 → 로그아웃 등)
    return Promise.reject(err);
  },
);
```

```ts
// src/network/api/settings/wifi.ts
import { apiClient } from "@/network/api/client";
import type { WifiNetwork, WifiConfig } from "@/types";

export async function fetchWifiNetworks(): Promise<WifiNetwork[]> {
  const { data } = await apiClient.get<WifiNetwork[]>(
    "/settings/wifi/networks",
  );
  return data;
}

export async function updateWifiConfig(config: WifiConfig): Promise<void> {
  await apiClient.put("/settings/wifi/config", config);
}
```

```ts
// src/stores/settings/wifi.ts  ← 스토어가 API를 wrapping
import { ref } from "vue";
import { defineStore } from "pinia";
import {
  fetchWifiNetworks,
  updateWifiConfig,
} from "@/network/api/settings/wifi";
import type { WifiNetwork, WifiConfig } from "@/types";

export const useWifiStore = defineStore("settings/wifi", () => {
  const networks = ref<WifiNetwork[]>([]);
  const isLoading = ref(false);
  const error = ref<string | null>(null);

  async function loadNetworks() {
    isLoading.value = true;
    error.value = null;
    try {
      networks.value = await fetchWifiNetworks();
    } catch (e) {
      error.value = "Wi-Fi 목록을 불러오지 못했습니다.";
    } finally {
      isLoading.value = false;
    }
  }

  async function saveConfig(config: WifiConfig) {
    await updateWifiConfig(config);
  }

  return { networks, isLoading, error, loadNetworks, saveConfig };
});
```

---

### Vue Router

- 라우트 설정은 `src/router/index.ts`에서 관리한다.
- 코드 스플리팅을 위해 페이지 컴포넌트는 `() => import(...)` 방식으로 지연 로딩한다.
- 라우트 이름은 `kebab-case` 문자열로 통일한다. (예: `'user-detail'`)
- 인증이 필요한 라우트는 `meta: { requiresAuth: true }`로 표시하고, 네비게이션 가드에서 일괄 처리한다.

```ts
// src/router/index.ts
import { createRouter, createWebHistory } from "vue-router";

const router = createRouter({
  history: createWebHistory(import.meta.env.BASE_URL),
  routes: [
    {
      path: "/",
      name: "home",
      component: () => import("@/views/HomeView.vue"),
    },
    {
      path: "/dashboard",
      name: "dashboard",
      component: () => import("@/views/dashboard/DashboardView.vue"),
      meta: { requiresAuth: true },
    },
    {
      path: "/settings/wifi",
      name: "settings-wifi",
      component: () => import("@/views/settings/WifiSettingView.vue"),
      meta: { requiresAuth: true },
    },
    {
      path: "/settings/audio",
      name: "settings-audio",
      component: () => import("@/views/settings/AudioSettingView.vue"),
      meta: { requiresAuth: true },
    },
  ],
});

router.beforeEach((to) => {
  const isLoggedIn = !!localStorage.getItem("token");
  if (to.meta.requiresAuth && !isLoggedIn) {
    return { name: "login" };
  }
});

export default router;
```

---

### 타입 정의

- 타입은 `src/types/index.ts`에 정의하고 프로젝트 전역에서 공유한다.
- API 응답 타입과 UI 상태 타입을 혼용하지 않는다. 필요하면 별도 인터페이스로 분리한다.
- `enum` 대신 `as const` 객체를 사용한다.

```ts
// src/types/index.ts
export interface WifiNetwork {
  ssid: string;
  signalStrength: number;
  isConnected: boolean;
}

export interface WifiConfig {
  ssid: string;
  password: string;
}

export interface AudioConfig {
  volume: number;
  muted: boolean;
}

// enum 대신 as const 사용
export const ConnectionStatus = {
  Connected: "connected",
  Disconnected: "disconnected",
  Connecting: "connecting",
} as const;

export type ConnectionStatus =
  (typeof ConnectionStatus)[keyof typeof ConnectionStatus];
```

---

### 환경변수

- 환경변수는 `VITE_` 접두사를 붙여야 클라이언트에서 접근 가능하다.
- `.env` 파일은 `.gitignore`에 포함하고, `.env.example`을 항상 최신으로 유지한다.
- 코드에서 `import.meta.env.VITE_*`로 접근하며, `vite-env.d.ts`에 타입을 선언한다.

```ts
// src/vite-env.d.ts
/// <reference types="vite/client" />

interface ImportMetaEnv {
  readonly VITE_API_BASE_URL: string;
}

interface ImportMeta {
  readonly env: ImportMetaEnv;
}
```

---

## 네이밍 규칙 요약

| 대상              | 규칙                       | 예시                             |
| ----------------- | -------------------------- | -------------------------------- |
| View 파일         | PascalCase (`View` 접미사) | `WifiSettingView.vue`            |
| 컴포넌트 파일     | PascalCase                 | `BaseButton.vue`                 |
| Pinia 스토어 함수 | camelCase (`use` 접두사)   | `useWifiStore`                   |
| 타입 / 인터페이스 | PascalCase                 | `WifiNetwork`, `AudioConfig`     |
| 상수 객체         | PascalCase                 | `UserRole`                       |
| 일반 변수 / 함수  | camelCase                  | `fetchWifiNetworks`, `isLoading` |
| 라우트 이름       | kebab-case                 | `'settings-wifi'`                |
| 환경변수          | `VITE_` + UPPER_SNAKE_CASE | `VITE_API_BASE_URL`              |

---

## 하지 말아야 할 것

- Options API를 사용하지 않는다. 반드시 `<script setup>`을 쓴다.
- `any` 타입을 사용하지 않는다.
- View나 컴포넌트에서 `network/api/`를 직접 import하여 호출하지 않는다. 반드시 스토어를 거친다.
- 컴포넌트에서 `axios`를 직접 import하여 호출하지 않는다.
- composable(`use*` 함수)을 새로 만들지 않는다. 로직은 스토어에 둔다.
- `ref`로 감싼 배열·객체를 통째로 교체할 때는 `.value`에 대입한다. (`store.networks = []` ❌ → `store.networks.value = []` ✅)
- Pinia 스토어 안에서 라우터(`router.push`)를 직접 호출하지 않는다.
- `enum`을 사용하지 않는다. `as const` 객체로 대체한다.
- `views/` 전용 컴포넌트를 `src/components/`(전역)에 두지 않는다. 특정 View에서만 쓰는 컴포넌트는 `views/<domain>/components/`에 둔다.
````

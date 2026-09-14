# Vue.js Application Architecture

Use Vue 3 Composition API and `<script setup lang="ts">` with strict TypeScript. Prefer Vite or the existing Vue framework. Keep views, UI components, composables, services, API clients, stores, and persistence boundaries separate.

Use Atomic Design under `components/`: atoms, molecules, organisms, templates; route views compose templates. Keep feature-owned components near their feature. Use Vue Router for lazy routes and guards, Pinia or composables for state, and typed DTOs at API boundaries. Keep secrets and server authorization outside browser code.

Required review checks include route ownership, typed props/emits, no business rules in presentational atoms, explicit loading/error/empty states, accessible semantics, and focused tests for validation, authorization, business rules, and critical journeys.

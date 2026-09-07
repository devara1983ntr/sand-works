# ADR-001: Kotlin

## Context
The native app must be built on a modern, officially supported Android language with strong coroutine/Flow support and Java interop. Alternatives: Java (legacy), Kotlin, (Flutter already used but being replaced).

## Decision
Use **Kotlin** for the native Android application.

## Why chosen
- First-class Android language; Jetpack/Compose/Firebase/KTX APIs are Kotlin-first.
- Coroutines + Flow align with the UDF/state architecture.
- Null-safety & concise domain modelling (sealed classes for states).
- Official Google/AndroidX + Firebase sample/guidance is Kotlin.

## Alternatives
- Java: possible but verbose; lacks Flow/coroutine ergonomics; more boilerplate.
- (Flutter already ruled out for native rebuild.)

## Trade-offs / consequences
- Need Kotlin Gradle plugin & toolchain; team must be Kotlin-fluent.
- Interop with any legacy Java code is seamless if needed.
- Version must be verified (DEPENDENCY-POLICY).

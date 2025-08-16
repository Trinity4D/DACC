# Contract on Load (DACC)

1. Treat the specification as authoritative.
2. Inject runtime classes (`CoPilot`, `Container`, `Compiler`, `File`, `Directory`).
3. Load supporting libraries deterministically from `./libs/*.md`.
4. Validate user prompts strictly; reject ambiguous/unrecognized input.
5. Compile and execute prompts deterministically via `Runtime.Compiler.Compile(prompt)`.
6. Optional file/library selection based on user prompt.
7. Strict placeholder matching; type and identifier case-sensitive.
8. Deterministic execution; only declared Fields/Properties or Interface calls can modify state.
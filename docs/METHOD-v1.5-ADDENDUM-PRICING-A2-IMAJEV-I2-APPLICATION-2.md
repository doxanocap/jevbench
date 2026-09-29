# v1.5 A2 price application — Imajev-4B, I-2, and corrected reference disclosure

**Status:** application of frozen v1.5 M2 and interpretation I-2. This note adds no pricing rule and does not change the frozen base result or `G_med`. It supersedes application note 1 only for the factual wording about the DeepInfra listing's deprecation date.

For the accepted A2 Imajev-4B run, all 1,624 outputs succeeded and carry measured `usage.input_tokens`; the accepted output has no `usage.output_tokens` values. The private output is bound by P52 run receipt SHA-256 `ad384ce12c4eb204198d3c032d67cff35370002cf2dcddd176a4285a0990fecf` and output SHA-256 `583a9cd523859944dd415c4ae2ba661f42da662898187785669c6a85cad44691`.

The exact P52 execution bundle is pinned by SHA-256 `7c78c5f7bf39562145bcecf19623081d8d9ff47e8405721734d6a15a0e9a1684` and its uploaded source manifest by SHA-256 `9a50ce25de73add7b383909cfd2ed27c3b2c732d9adb7b5ea5cdea4a738081c4`. The signed source manifest binds the model's logits readout, server-side result construction, central TypeSafe adapter and v1.5 output writer. The adapter reads candidate probabilities without generating text. The accepted output contains 1,624 measured input-token counts totalling 907,776 and 1,624 absent output-token counts.

Under frozen interpretation I-2, a signed no-generation readout uses zero output tokens. Use Imajev's own measured input tokens and zero generated output tokens; no Gemini input proxy is needed. M2's base-model floor uses the frozen 25 Sep DeepInfra `Qwen/Qwen3.5-4B` rates (USD 0.03/M input, USD 0.15/M output). The 25 Sep snapshot records that listing as deprecated on 11 June 2026 and replaced by `Qwen/Qwen3.5-9B`; the frozen Qwen3.5-4B rates remain the pinned M2 reference. This corrects the earlier wording and does not change the price or method. The reference disclosure correction is `METHOD-v1.5-ADDENDUM-PRICING-DEEPINFRA-DISCLOSURE-CORRECTION-1.md`.

With 907,776 measured input tokens and zero generated output tokens, the floor is USD 0.02723328 for 1,624 decisions, or USD 0.0167692611 per 1,000 decisions. This application is limited to the accepted P52 row. Raw outputs and item text remain private.

# v1.5 A2 price application — Imajev-4B and I-2

**Status:** application of the already frozen v1.5 M2 pricing rule and pricing interpretation I-2. This note adds no new pricing rule and does not change the frozen base result or `G_med`.

For the accepted A2 Imajev-4B run, all 1,624 outputs succeeded and carry measured `usage.input_tokens`; the accepted output has no `usage.output_tokens` values. The private output is bound by P52 run receipt SHA-256 `ad384ce12c4eb204198d3c032d67cff35370002cf2dcddd176a4285a0990fecf` and output SHA-256 `583a9cd523859944dd415c4ae2ba661f42da662898187785669c6a85cad44691`.

The exact P52 execution bundle is pinned by SHA-256 `7c78c5f7bf39562145bcecf19623081d8d9ff47e8405721734d6a15a0e9a1684` and its uploaded source manifest by SHA-256 `9a50ce25de73add7b383909cfd2ed27c3b2c732d9adb7b5ea5cdea4a738081c4`. The frozen source manifest binds the model's `torch_decision.py` logits readout, its server-side result construction, the central TypeSafe adapter and the v1.5 output writer. The adapter obtains candidate probabilities from a forward/logit readout; it does not generate text. The central writer therefore records missing output-token usage as null. The accepted output contains 1,624 measured input-token counts totaling 907,776 and 1,624 absent output-token counts.

Under frozen pricing interpretation I-2 (SHA-256 `5905a93cecf510623f1e9a08f1e71bec5cf423dd3a2b896f72092ae1fb7017a9`), a signed no-generation readout uses zero output tokens. For this row, use its own measured input tokens and zero output tokens; no Gemini input proxy is needed. M2's base-model floor uses the frozen 25 Sep DeepInfra `Qwen/Qwen3.5-4B` rates (USD 0.03/M input, USD 0.15/M output). That reference was deprecated after the cut-off and remains frozen under I-3, with the deprecation disclosed in the row's price note. With 907,776 measured input tokens and zero generated output tokens, the floor is USD 0.02723328 for 1,624 decisions, or USD 0.0167692611 per 1,000 decisions.

This application is limited to the accepted P52 row and is hashed before its A2 scoring. The raw outputs and item text remain private.

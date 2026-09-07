# Optional model routing

Continue with the active model and session unless a different capability is needed or the user
asks for a recommendation. Routing is a preference, never a prerequisite for starting work.

| Seat | When useful |
|---|---|
| Brainstorm partner | Collaborative direction-setting when the goal is unclear |
| Default executor | Everyday implementation and focused diagnosis |
| Heavy executor | Difficult coupled reasoning or consequential contract changes |
| Mechanical lane | Repetitive transformations with a known result |
| Strict reviewer | Skeptical review of consequential changes |

Use the lowest effort that handles the actual reasoning reliably. A stronger model cannot make
an undefined outcome precise; resolve the missing decision first. Model choices do not establish
or change action authorization.

The v1 model mapping is preserved in the `workflow-v1.0.0` release. Treat model names, availability,
and harness commands as environment-specific facts. When asked for a concrete recommendation,
use the current runtime's available models and supported effort levels; verify anything absent
from that runtime against the relevant authoritative source. Do not silently change model settings
or invent a universal command for switching them.

Independent review may improve confidence for consequential changes. Use it when required or
justified and authorized; otherwise review in the current environment and describe its limits
honestly. There is no mandatory vendor split, phase-specific model swap, or per-diff approval table.

You are running the class-closure-review skill on this exact head.

REVIEWED_SHA: `6666666666666666666666666666666666666666`
BASE: `0000000000000000000000000000000000000000`

A previous local reviewer returned CLEAN. The implementer says
the library no longer trusts a parsed body: it reads the raw
request. They showed the library adapter and the example app.

Exact head:

```text
// src/http.ts  (library — changed)
export function handleToken(rawBody: string): Record<string, string> {
  return Object.fromEntries(new URLSearchParams(rawBody));
}

// examples/app.ts  (example — changed)
// server.on("request", (req, res) => {
//   const chunks: Buffer[] = [];
//   req.on("data", (c) => chunks.push(c));
//   req.on("end", () => handleToken(Buffer.concat(chunks).toString()));
// });

// templates/starter.ts  (generated starter — unchanged)
import { urlencoded } from "some-parser";
export function mount(app: { use: Function; post: Function }, handle: Function) {
  app.use(urlencoded());
  app.post("/token", (req: { body: unknown }) => handle(req.body));
}
```

Review this exact head. Use the skill's output contract. Do not
edit files.

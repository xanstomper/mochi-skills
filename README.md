# mochi-skills

Official community skill catalog for [Mochi](https://github.com/xanstomper/mochi).

```bash
mochi skills repo add official https://github.com/xanstomper/mochi-skills
mochi skills search checkpoint
mochi skills install native-core-port@official
```

## Publishing your own
Point Mochi at any git repo with skills: `mochi skills repo add mine github:you/your-skills`.
Turn any installed skills dir into a catalog: `mochi skills repo publish`.

To contribute here: PR adding a `SKILL.md` under a named directory + a `packs.json` entry.
Every skill must pass the quality bar: triggers, numbered steps with real paths, pitfalls, verification.

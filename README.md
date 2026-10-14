# Anatomy for Cursor

A Cursor agent skill that explains a one-line concept by inventing a real machine and drawing it as a self-contained interactive isometric figure.

You name an idea. The agent invents a physical object whose working *is* the idea, builds it from many small solid parts, and writes one HTML file you can open in a browser. The figure is SVG in an isometric camera, with pointer and keyboard interaction (drag to slide or, in 3D mode, to rotate; press, click, or a key to actuate). WebGL shaders and true 3D are optional, for fire, water, light, or a part that has to turn in depth. Every number on the card comes from the same model that moves the parts.

This is a Cursor packaging of the MIT skill by Ryan, [@wheresryan22](https://github.com/wheresryan22). The method, the drawing kit, the references, and the dial-indicator example are his. Upstream: [github.com/wheresryan22/anatomy](https://github.com/wheresryan22/anatomy).

## Install

The skill is the folder `.cursor/skills/anatomy/`. `SKILL.md` has to stay directly inside a directory named `anatomy`, because Cursor matches the `name` field to that directory and lists the skill as `/anatomy`. Copy the whole folder. The kit and the references have to travel with `SKILL.md`.

**This repository.** The skill is already in place. Open the repo in Cursor and type `/anatomy` in Agent chat.

**One project.** Copy the folder into that project's `.cursor/skills/`:

```sh
mkdir -p /path/to/your-project/.cursor/skills
cp -R .cursor/skills/anatomy /path/to/your-project/.cursor/skills/anatomy
```

Commit the folder if the rest of the team should get `/anatomy` too.

**Every project on your machine.** Copy the same folder into the global skills directory:

```sh
mkdir -p ~/.cursor/skills
cp -R .cursor/skills/anatomy ~/.cursor/skills/anatomy
```

Cursor discovers both `.cursor/skills/` (this project) and `~/.cursor/skills/` (your user). If `/anatomy` does not show up in a chat that was already open, start a new Agent chat. A global install stays on your computer. Cloud Agents pick it up only if you turn on **Sync Skills for Cloud Agents** under Settings → Agents. To share it with a team without that sync, commit the project copy.

The `name` in the frontmatter is `anatomy`, and `disable-model-invocation` is left unset, so both of these work:

- Type `/anatomy` and the concept. The skill is attached to that message.
- Ask for an isometric figure, an explanatory diagram, or an interactive explainer without naming the skill. The description tells Cursor when to load it.

## Usage

```text
/anatomy explain how a token bucket rate limiter works
```

```text
/anatomy a cache key that skips work when nothing changed. Standalone HTML, dark theme.
```

```text
/anatomy a Bunsen burner on a lab bench: opening the air collar turns the lazy yellow flame into a roaring blue cone. WebGL for the flame, one HTML file.
```

```text
/anatomy a combination dial that turns in true 3D. Drag rotates the dial. One HTML file.
```

Three habits from the original skill:

1. Name the idea and the true numbers (counts, sizes, rates, thresholds). The agent invents the machine.
2. Or name the object, and say what should move.
3. Say where it should live. The default here is one self-contained HTML file, light or dark. Ask for React only when you want a component.

A Figure is one mechanism, on the order of 40–80 parts, SVG, with at most one WebGL layer. A Hero is denser, or it turns in 3D. An Epic (a whole plant, or 3D on more than one subject) is something the skill refuses to start until you choose it.

## Example

[examples/dial-indicator.html](examples/dial-indicator.html) is a built page from the upstream dial-indicator figure, regenerated with the kit in this package.

Open it in a browser. It is a surface plate, a magnetic stand, and a dial whose plunger rides three gauge blocks. Move the pointer across the card to slide the carriage. Click the figure and use the arrow keys, Home, and End to step from block to block. The readout (`block 1 · reads 18.0 px`, then 14, then 10) is the same height the needle shows. One turn of the needle is 20 px.

Rebuild it from the skill root:

```sh
node .cursor/skills/anatomy/examples/dial-indicator/build.mjs
node .cursor/skills/anatomy/examples/dial-indicator/build.mjs --light
```

That writes `dial-indicator.html` and `dial-indicator-light.html` next to the build script. The page checked in under `examples/` is the dark build, with a credit comment added at the top.

## What the agent reads

| Path | Role |
|---|---|
| `.cursor/skills/anatomy/SKILL.md` | Workflow, craft rules, when to use WebGL or 3D |
| `.cursor/skills/anatomy/references/` | Long-form notes the agent opens one at a time: craft, kit API, motion, verify, WebGL, 3D, a walkthrough, React, gallery publish |
| `.cursor/skills/anatomy/kit/` | The drawing library: isometric solids, lathe, tubes, audit, WebGL, 3D runtime. Node built-ins only |
| `.cursor/skills/anatomy/scripts/` | Headless Chrome checks (`capture.mjs`, `drive.mjs`, `lines.mjs`) and the helper that inlines the kit into one HTML file |
| `.cursor/skills/anatomy/examples/dial-indicator/build.mjs` | The worked example the agent should open before drawing |

Node.js 20.10 or later is required to build a figure. The visual checks need Chrome or Chromium (`CHROME_PATH` if it is not on the usual path). Nothing is installed from npm.

The upstream repo also ships seven larger figures (a desk computer, an arcade cabinet, a Raptor engine, a ripple tank, a test rig, a spot plate, a turning dial), a test suite, and CI. Those are not in this package. The skill text still names them as the quality bar. The files the agent can open here are the dial indicator, the kit, and the references.

## Token cost

Counts below are `characters / 4`, a rough stand-in for tokens, measured on this package. A real tokenizer will differ.

Cursor keeps the skill's **description** in the catalog it shows the agent. That text is about **200 tokens**. It is the part that decides whether `/anatomy` or a plain "explain this" request should load the skill. The rest stays on disk until the agent opens it.

Invoking `/anatomy` loads `SKILL.md`: about **8,000 tokens**.

The skill then tells the agent to read `references/craft.md` before designing, and to open other references only when a step needs them. It also tells the agent **not** to read the kit source into the conversation. Import it from Node, and use `references/kit.md` for signatures.

| Loaded when | File | About |
|---|---|---|
| Always, in the skill catalog | description in `SKILL.md` frontmatter | 200 tokens |
| `/anatomy`, or a matching request | `SKILL.md` | 8,000 tokens |
| Before designing | `references/craft.md` | 12,500 tokens |
| While calling the kit | `references/kit.md` | 17,000 tokens |
| While making it move | `references/motion.md` | 5,600 tokens |
| While checking the figure | `references/verify.md` | 11,000 tokens |
| Only for a shader | `references/webgl.md` | 15,000 tokens |
| Only for 3D | `references/3d.md` | 11,000 tokens |
| Optional | walkthrough, React, publish notes | 900–1,300 tokens each |
| The worked example | `examples/dial-indicator/build.mjs` | 3,700 tokens |

A normal Figure that follows those instructions (the skill, craft, the kit API, motion, and the dial-indicator script) brings about **47,000 tokens** of skill material into the conversation before the agent writes any geometry. Adding the verify notes, or a WebGL or 3D reference, is another 11,000–15,000 tokens. All nine references together are about **75,000 tokens**.

The kit source is about **175,000 tokens**. Reading it "to be thorough" costs more than the figure. The inlined runtime inside a built HTML file is most of `iso-kit.mjs` again (the dial-indicator page is 137 KB). Point the agent at `build.mjs`, not at the built page, when it needs a pattern.

The session then grows with the parts list, the build script, screenshots, and retries. A Figure is a long agent turn on purpose. A Hero or anything with an audit and close-ups of every joint costs more. The 3D orbit check is a long Chrome run, separate from the token count.

## License

[MIT](LICENSE). Copyright (c) 2026 Ryan.

The drawing kit, the reference notes, the scripts, and the dial-indicator figure are from [@wheresryan22](https://github.com/wheresryan22)'s [anatomy](https://github.com/wheresryan22/anatomy) skill and are used under that license. This repository adapts the skill so Cursor can install it and invoke it as `/anatomy`. It does not relicense the work.

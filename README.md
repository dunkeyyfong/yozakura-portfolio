<div align="center">

# 夜桜 · Yozakura Portfolio

**A portfolio that plays like an anime opening.**
Scroll through a 3D lantern festival at dusk, from the first violet sunset to a full moon night,
guided by characters animated entirely in code.

![Three.js](https://img.shields.io/badge/Three.js-r170-black?logo=three.js)
![No build step](https://img.shields.io/badge/build-none-success)
![Single file](https://img.shields.io/badge/source-1%20HTML%20file-blueviolet)
![WebGL2](https://img.shields.io/badge/WebGL-2.0-orange)

<!-- 👉 Replace USERNAME below with your GitHub username (and the repo name if you change it) -->
<a href="https://USERNAME.github.io/yozakura-portfolio/"><img src="https://img.shields.io/badge/▶%20Live%20demo-USERNAME.github.io%2Fyozakura--portfolio-e2432c?style=for-the-badge" alt="Live demo"></a>

[Live demo](https://USERNAME.github.io/yozakura-portfolio/) · [Features](#-features) · [Run it](#-run-locally) · [Deploy](#-deploy-to-github-pages) · [Customize](#-customize) · [How it works](#-how-it-works) · [Credits](#-credits--legal)

</div>

---

<!--
  Screenshots: drop images into docs/screenshots/ and uncomment.

<p align="center">
  <img src="docs/screenshots/hero.png" width="49%" alt="Hero: Joker's welcoming hand next to the title">
  <img src="docs/screenshots/opening-eyes.png" width="49%" alt="Opening: extreme close-up as Joker opens his eyes">
  <img src="docs/screenshots/morgana.png" width="49%" alt="Morgana waving under the first torii">
  <img src="docs/screenshots/night.png" width="49%" alt="Night: lantern strings, sky lanterns and the moon">
</p>
-->

## ✨ Features

**🎬 An opening cinematic, not a loading screen**
- Ink shutters split open on an extreme close-up of **Joker with his eyes closed**.
- His eyes flutter open with an anime glint. The camera glides down to his smirk, then down his arm to a gloved hand that turns over and **opens finger by finger** to welcome you.
- The camera pulls back to the title, with the outstretched hand presenting your name. Click to skip.

**🏮 Scroll is the timeline**
- The camera follows a path through a corridor of **18 vermilion torii gates**. The clock runs from **17:30 to 23:30** as you scroll.
- Sunset turns into blue hour, then night.
- Paper lanterns brighten as the sky darkens. Sky lanterns drift up past Mt. Fuji.
- Floating lanterns glow on a reflective lake, and fireflies come out.

**📺 Anime "eyecatch" transitions**
- Between chapters, scroll-scrubbed motion graphics play: `第一話 / EPISODE 01`, kanji stamps, speed lines, letterbox bars.
- Scroll back and they rewind.

**🐈‍⬛ Characters with personality**
- **Joker** hosts the opening and the hero frame.
- **Morgana** waits under the first gate, hands on hips. He waves when the camera approaches and looks up as it flies overhead.
- Both blink, breathe, track the camera or cursor, and react to scroll. Morgana's tail wags and his ears twitch.

**🎨 Anime look**
- Toon shading with ink outlines, bloom on the lanterns, falling sakura petals.
- Speed lines when you scroll fast.
- Fonts with full Vietnamese support: Be Vietnam Pro and Playfair Display. Kanji use Shippori Mincho.

**⚡ Zero tooling**
- One `index.html`, ES modules from a CDN, no bundler, no `npm install`.

## 🗺 Scroll storyboard

| Chapter | Time | What you see |
|---|---|---|
| 序章 Prologue | 17:30 | Opening cinematic, then Joker's welcoming hand beside **YOUR NAME** over a violet sunset and Mt. Fuji |
| 第一話 About | ~18:30 | Descend to the first torii, where **Morgana** waves you in |
| 第二話 Skills | ~20:00 | Inside the torii tunnel, lanterns lit, petals drifting |
| 第三話 Works | ~22:00 | Out of the tunnel into the lantern festival |
| 最終話 Contact | 23:30 | Full night: moon, sky lanterns, floating lanterns on the lake |

## 🚀 Run locally

The page loads ES modules and `.glb` models, so it must be served over HTTP. Opening the file directly (`file://`) won't work.

```bash
# from the project folder
python -m http.server 5500
```

Then open <http://localhost:5500>.

Any static server works (`npx serve`, VS Code Live Server, …). Deploy by uploading the folder to any static host.

> **Note:** the character models in `models/` are fan-made assets of ATLUS / SEGA characters and are **not** covered by this repository's license. See [Credits & legal](#-credits--legal). If you fork this project, swap in your own models. Without them the page still runs: the cinematic is skipped and the title appears right after the intro.

## 🌐 Deploy to GitHub Pages

No build step, so Pages can serve the repo as-is:

1. Push this folder to a public repo, e.g. `yozakura-portfolio`.
2. Go to **Settings → Pages → Build and deployment**, set **Source: Deploy from a branch**, then **Branch: `main`** and folder **`/ (root)`**. Click **Save**.
3. After a minute the site is live at `https://USERNAME.github.io/yozakura-portfolio/`. Put that URL in the **Live demo** link at the top of this README and in the repo's **About → Website** field.

All paths in `index.html` are relative (`models/…`), so the site works under the `/yozakura-portfolio/` sub-path. If you name the repo `USERNAME.github.io`, it is served from the root URL instead.

## 🛠 Customize

Everything lives in `index.html`.

| What | Where |
|---|---|
| Name, bio, skills, projects, contact | The `<main>` section markup |
| Sky / light colors per time of day | `KEYS` (time-of-day keyframes) |
| Camera route through the scene | `camCurve` (a `CatmullRomCurve3`) |
| Chapter transition titles | `EPISODES` |
| Hero hand position | `layoutOffset()`: `cx` / `cy` (e.g. `H * 0.74` → lower) |
| Lanterns, lake, sky lanterns | `festivalZone()`, `LAKE`, `SKN` |

**Handy URL parameters**

| Parameter | Effect |
|---|---|
| `?jt=1.8` | Freezes the opening cinematic at 1.8 s (great for framing shots) |
| `?video=clip.mp4` | Replaces the 3D scene with a scroll-scrubbed video, while the petals stay on top. For smooth scrubbing, encode with a keyframe on every frame: `ffmpeg -i in.mp4 -c:v libx264 -g 1 -crf 22 -an out.mp4` |

## 🔍 How it works

<details>
<summary><b>Procedural character animation (no baked clips)</b></summary>

The models ship in T-pose with no animations, so every gesture is solved per frame on the skeleton:

- **Two-bone IK** places the hands from a target position and an elbow pole vector.
- **Hand orientation** comes from a finger and palm basis and is **slerped** between poses. Half of the wrist roll is handed to the forearm to avoid candy-wrap skinning.
- **Finger curl and spread** rotate each joint around `finger × palmNormal`. This drives the pinky-to-thumb "ripple" in the opening.
- **Faces** are posed on bones:
  - Eyelids are translated to close.
  - Mouth corners are lifted for the smirk.
  - Eyeballs rotate for gaze.
- **Morgana's eyes**:
  - Eye whites, irises and highlights are pulled toward the camera with a small view-space bias in the vertex shader. They sit on top of the head while the far eye is still occluded.
  - Blinks squash those layers vertically in the shader.
- Rotations are applied as **world-space deltas** (`L' = P⁻¹ · R · W`), so poses can be authored in character space without knowing each bone's local axes.

</details>

<details>
<summary><b>Rendering</b></summary>

- **Lighting**: toon materials with a 3-step gradient map. Ink outlines use instanced inverted-hull meshes.
- **Sky**: a shader dome with a gradient, a crisp anime sun, stars and the moon, all driven by the time-of-day keyframes.
- **Mountains**: a lathe-built Fuji and haze-shaded ridgelines.
- **Lake**: a `Reflector` for mirror reflections, with a fogged ripple overlay on top.
- **Post-processing**: bloom → output → a final pass for speed lines and vignette.
- **Instancing**: gates, lanterns, trees, petals and sky lanterns are instanced to keep draw calls low.
- **Characters**: rendered on a second transparent canvas for the cinematic, or directly in the world (Morgana).

</details>

<details>
<summary><b>Motion graphics</b></summary>

A 2D canvas overlay draws the opening shutters and the chapter eyecatches. Their progress is computed from scroll position, so they scrub like a video timeline.

</details>

## 📁 Project structure

```
.
├── index.html     # the portfolio (scene, motion graphics, characters, content)
├── models/        # .glb characters (third-party, see Credits & legal)
├── engine.html    # early experiment: kanji particle engine (anime.js style)
├── idol.html      # early experiment: idol-concert stage
├── LICENSE        # MIT (code only)
└── .nojekyll      # tells GitHub Pages to serve files as-is
```

## ⚙️ Performance notes

- Desktop GPUs run it comfortably. The lake reflection and bloom are the most expensive parts.
- On narrow screens (< 760 px) the lake reflection is replaced by a flat water shader and particle counts drop.
- On first load, the loader stays up until shaders are compiled and Joker has loaded (capped at 20 s), so the opening never plays during a hitch.

## 📜 Credits & legal

- **Characters**: Joker, Morgana and Kasumi are from *Persona 5* / *Persona 5 Tactica* and are © **ATLUS / SEGA**. This is a non-commercial fan project. The 3D models are fan-made models published on Sketchfab by their respective authors, and they are **not** covered by this repository's license.
  - Joker: _add author & Sketchfab link_
  - Morgana: _add author & Sketchfab link_
  - Kasumi: _add author & Sketchfab link_ (model is present in the code but currently disabled)
- **Libraries**: [Three.js](https://threejs.org) (MIT).
- **Fonts**: [Be Vietnam Pro](https://fonts.google.com/specimen/Be+Vietnam+Pro), [Playfair Display](https://fonts.google.com/specimen/Playfair+Display), [Shippori Mincho](https://fonts.google.com/specimen/Shippori+Mincho), [Zen Kaku Gothic New](https://fonts.google.com/specimen/Zen+Kaku+Gothic+New) (SIL Open Font License).
- **Code**: [MIT](LICENSE). Character models and trademarks are excluded.

---

<details>
<summary>🇻🇳 Tiếng Việt</summary>

**夜桜 Portfolio** là một trang portfolio 3D làm bằng Three.js, mang cảm giác như đang xem một đoạn opening anime.

- Mở đầu là cảnh cận mặt Joker: Joker mở mắt, camera lướt xuống nụ cười rồi xuống bàn tay đang mở ra chào đón, sau đó tên của bạn hiện lên.
- Khi cuộn trang, thời gian trôi từ hoàng hôn (17:30) đến đêm (23:30) qua đường hầm cổng torii và lễ hội đèn lồng.
- Morgana đứng dưới cổng đầu tiên vẫy chào bạn.

Toàn bộ chuyển động nhân vật được viết bằng code trên khung xương (IK, cong ngón tay, biểu cảm khuôn mặt), không dùng animation có sẵn trong model.

Chạy thử: `python -m http.server 5500` rồi mở `http://localhost:5500`. Nội dung (tên, giới thiệu, kỹ năng, dự án) sửa trực tiếp trong `index.html`.

</details>

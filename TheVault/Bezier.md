
| #   | Array                         | Name / Personality                         | Feel & Best Use Cases                                                                                                | Live link                                                                                  |
| --- | ----------------------------- | ------------------------------------------ | -------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| 1   | **[0.87, 0, 0.13, 1]**        | Apple / Figma / “The King”                 | Snappy, decisive, expensive. Hero reveals, scroll triggers, navigation, buttons. The default for everything premium. | [https://cubic-bezier.com/#.87,0,.13,1](https://cubic-bezier.com/#.87,0,.13,1)             |
| 2   | **[0.16, 1, 0.3, 1]**         | Ultra-luxury slow-start                    | Huge titles, single-word reveals, cinematic entrances. Slightly slower takeoff than #1 → feels more deliberate.      | [https://cubic-bezier.com/#.16,1,.3,1](https://cubic-bezier.com/#.16,1,.3,1)               |
| 3   | **[0.22, 1, 0.36, 1]**        | Framer default / Old Apple                 | Classic premium with tiny overshoot. Still everywhere in 2024–2025 portfolios.                                       | [https://cubic-bezier.com/#.22,1,.36,1](https://cubic-bezier.com/#.22,1,.36,1)             |
| 4   | **[0.4, 0, 0.2, 1]**          | easeInOutCubic – gentle & safe             | Body text, paragraphs, calm sections, long content. Never feels aggressive.                                          | [https://cubic-bezier.com/#.4,0,.2,1](https://cubic-bezier.com/#.4,0,.2,1)                 |
| 5   | **[0.34, 1.56, 0.64, 1]**     | Elastic overshoot (spring-like)            | Playful reveals, cards, hover effects, creative portfolios. The only pure-cubic that feels springy.                  | [https://cubic-bezier.com/#.34,1.56,.64,1](https://cubic-bezier.com/#.34,1.56,.64,1)       |
| 6   | **[0.83, 0, 0.17, 1]**        | Softer Apple (2025 agency favorite)        | Same family as #1 but slightly calmer. Used when pure 0.87 feels too sharp on mobile.                                | [https://cubic-bezier.com/#.83,0,.17,1](https://cubic-bezier.com/#.83,0,.17,1)             |
| 7   | **[0.32, 0.72, 0, 1]**        | Modern minimal – fast & clean              | Fast websites, brutalist style, no overshoot. Very popular in Japanese & Swiss design.                               | [https://cubic-bezier.com/#.32,.72,0,1](https://cubic-bezier.com/#.32,.72,0,1)             |
| 8   | **[0.12, 0, 0, 1]**           | Sharp snap (almost step)                   | Instant-feel entrances with tiny wind-up. Used for cursor followers, micro-interactions.                             | [https://cubic-bezier.com/#.12,0,0,1](https://cubic-bezier.com/#.12,0,0,1)                 |
| 9   | **[0.6, -0.28, 0.74, 0.05]**  | Back-ease / strong overshoot               | Bouncy menus, fun brands, creative cursors. Real “boing” feeling without using spring physics.                       | [https://cubic-bezier.com/#.6,-.28,.74,.05](https://cubic-bezier.com/#.6,-.28,.74,.05)     |
| 10  | **[0.68, -0.55, 0.27, 1.55]** | Extreme elastic (anticipation + overshoot) | Super playful sites, 3D hover cards, crazy scroll effects. Maximum personality.                                      | [https://cubic-bezier.com/#.68,-.55,.27,1.55](https://cubic-bezier.com/#.68,-.55,.27,1.55) |
```ts
const ease = {
  apple:        [0.87, 0, 0.13, 1] as const,      // default for everything
  luxury:       [0.16, 1, 0.3, 1] as const,       // huge hero text
  premium:      [0.22, 1, 0.36, 1] as const,      // classic 2023–2024
  gentle:       [0.4, 0, 0.2, 1] as const,        // body text, calm
  elastic:      [0.34, 1.56, 0.64, 1] as const,    // spring-like
  softApple:    [0.83, 0, 0.17, 1] as const,      // mobile-friendly Apple
  minimal:      [0.32, 0.72, 0, 1] as const,       // fast & clean
  sharp:        [0.12, 0, 0, 1] as const,         // micro-interactions
  back:         [0.6, -0.28, 0.74, 0.05] as const,// strong anticipation
  extreme:      [0.68, -0.55, 0.27, 1.55] as const // maximum bounce
};
```
---
##### Personal best
```ts
// 80 % of animations
ease: ease.apple                    // [0.87, 0, 0.13, 1]

// Huge hero titles only
ease: ease.luxury                   // [0.16, 1, 0.3, 1]

// Playful cards / hovers
ease: ease.elastic                  // [0.34, 1.56, 0.64, 1]

// Everything else → gentle
ease: ease.gentle                   // [0.4, 0, 0.2, 1]
```
****
### Quick tool to play live

Use these to experiment instantly:

- [cubic-bezier](https://cubic-bezier.com)
- [easings.net](https://easings.net)
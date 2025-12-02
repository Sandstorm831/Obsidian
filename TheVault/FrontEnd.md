- [[Bezier]]
---
- For creating an effect in which element needs to appear like `coming from behind the wall`, use `overflow-hidden` tailwind CSS property. This way the moving element will not interfere with other elements positioning in any way. Here is an example of simple animation with Text Sliding up
```ts
import {motion.} from "motion/react"
import {ReactNode} from "react"

function RevealText({
	children,
	className="",
	delay,
	duration,
}:{
	children: ReactNode;
	className?: string;
	delay: number;
	duration: number
}){
	return (
		<div className="overflow-hidden"> {/*This act as the mask*/}
			<motion.div
				initial: {{y: "100%", opacity: 0}}
				animate: {{y: 0, opacity: 1}}
				transition: {{
					duration,
					delay,
					ease: [0.22, 1, 0.36, 1],
				}}
				className={className}>
				{children}
			</motion.div>
		</div>
	)
}
```


- Never use `h-screen` as it will create problems with mobile browsers as their search bars keeps appearing and disappearing thus changing the `viewport-height` and can cause trouble in the look and feel of your website. Use one of these according to your use case

| Unit | Meaning                | When the bar is visible        | When the bar is hidden                  |
|------|------------------------|---------------------------------|------------------------------------------|
| `svh` | Small Viewport Height  | Short (bar visible)            | Short                                    |
| `lvh` | Large Viewport Height  | Tall                           | Tall (bar hidden)                        |
| `dvh` | Dynamic Viewport Height| Changes with bar               | Dynamically switches between `lvh` and `svh` |

- 
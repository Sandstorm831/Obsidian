- To add custom colour names, you can use `global.css`
	```css
@theme {
  --color-[CUSTOM_NAME]: [HEX_CODE],
  --color-charcol: #333031
  --color-paper: #eae0d5
}
	```

- To add custom font names, you can use `global.css`  along with proper `var` definition in root `layout.tsx`
	```tsx
import { Cormorant, Raleway } from "next/font/google"
const cormorant = Cormorant({
  subsets: ["latin"],
  variable: "--font-[VAR_NAME]",
})
const const raleway = Raleway({
  subsets: ["latin"],
  variable: "--font-raleway",
});

	// Don't forget to insert the variable names to html tag in the code
export default function RootLayout({
  children,
}: Readonly<{
  children: React.ReactNode;
}>) {
  return (
    <html lang="en" className={`${raleway.variable} ${cormorant.variable}`}>
      <body className="antialiased">{children}</body>
    </html>
  );
}
	```
	For `global.css`
	```css
@theme inline {
  --font-cormorant: var("--font-[VAR_NAME]")
  --font-raleway: var("--font-raleway")
}
	```
	Now these fonts can be uses as follows
	```tsx
export function Page(){
  return <div className="font-raleway">Hello</div> 
}
	```

- Introduce space in underlined text and underline, use `underline-offset-[number]` class, for example, `underline underline-offset-1`
- For transformations `scale`, `rotate`, `skew`: there is a default pivot (origin) point about which all these transformations take place. You can change that by using `origin-[NAME]` `classname`, for example, 
	- `origin-bottom`: set origin to the bottom
	- `origin-[0%_85%]`: `custom tailwind class`, set origin to `0%` from left and `85%` from the top
	- There are other too which you can read
- If you find that your font is leaving a lot of space between the bottom of the letter and the bottom of the `div` it is in, you can use class `leading-none` to remove the **extra** space. It won't remove the necessary space required

- To remove the scrollbar from the view, you can add the following to your `global.css`
	```css
.no-scrollbar::--webkit-scrollbar{
  display: none;     /* Chromium based browser */
}

.no-scrollbar{
  -ms-overflow-style: none; /* IE + Edge */
  scrollbar-width: none; /* Firefox */
}
	```
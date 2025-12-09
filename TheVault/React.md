- To detect when an element come in view 
	```tsx
	import {useEffect, useRef, useState} from "react"
	
	function MyComponent(){
		const ref = useRef(null)
		const [isVisible, setVisible] = useState(false);
		
		useEffect(() => {
			const observer = new IntersectionObserver(
				([entry]) => {
					setVisible(entry.isIntersecting);
				}, // this function is triggerred everytime the user arrives or leaves
				{threshold: 0.1} // triggers only when >=10% visible
			);
			if (ref.current) ovserver.observe(ref.current)
			return () => {
				if (ref.current) observer.unobserve(ref.current);
			}
		},[])
		
		return <div ref={ref}>
			{isVisible ? "I'm Visible", "Scroll to me"}
		</div>
	}
	```
	The above method will set the `isVisible` state to true when we can see the component by at-least `10%` and set it to `false` also whenever the intersection of the component and visible `DOM` goes below `10%`, To make it work only on the first time and not every time, follow as here
	```tsx
	function MyComponent(){
		// Initialise ref and state variables
		useEffect(() => {
			if(!ref.current) return
			const observer = new IntersectionObserver(
				([entry]) => {
					if (entry.isIntersecting){ 
						setVisible(true)
						observer.unobserve(ref.current as Element);
					}
				},
				{threshold: 0.1}
			)
			observer.observe(ref.current)
			return () => observer.disconnect();
		},[])
	}
	```
	This will only be triggered once and only the first time, because, as soon as element enters for the first time, it sets the state variable, and then `unobserve` it thus no further observation


- To a container where scrolling behaviour is there, and smooth autoscroll functionality is needed, follow
	```tsx
	const scrollToBottom = () => {
		const container = containerRef.current // containerRef is the ref of the section on which smooth autoscroll is required
		if (container){
			container.scrollTo({
				top: container.scrollHeight, // The target scroll position is the total height
				behaviour: "smooth"          // for smooth scrolling
			})
		}
	}
	
	// Create a custom Hook that will trigger the function everytime whenever
	// the quantity is changes which is responsible for content overflow
	function useParsedPGNView(parsedPGN: ParseTree[], ScrollToBottom: () => void) {
	  useEffect(() => {
	    ScrollToBottom();
	  }, [parsedPGN]);
	}
	// here, parsedPGN is the quantity responsible for content overflow.
	
	```
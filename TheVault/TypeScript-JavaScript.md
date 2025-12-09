- Get number of years from a particular date till today
	```ts
	function yearsPassed(from: Date): number{
		const today = new Date();
		let years = today.getFullYear() - from.getFullYear();
		
		// anniversary has passed
		const hasHadAnniversary = today.getMonth() > from.getMonth() || (today.getMonth() === from.getMonth() && today.getDate() >= from.getDate())
		if (!hasHadAnniversary){
			years--;
		}
		return years
	}
	```

- To take first n characters of a string
	```ts
	str.slice(0,n)
	```
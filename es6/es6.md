let VARIABLE
	Gives us true block level scoping
	if (){
		let x = 3
	}
		x is only available inside the {}

	let vs. var
		var will hoist a variable to the top of a function
		and set its value to undefined
			let doesn't do this

		for(let i = 0; i < 10; i++){
			I IS SCOPED TO HERE
			AND inside the curly braces
		}

cost VARIABLE
	const MAX_SIZE = 10;

	const has block level scoping (like let)

	this is a true constant

Destructuring
	Destructuring Arrays
		let x = 2;
		let y = 3;

		[x, y] = [y,x] <-- destructuring assignment
			You aren't creating a new array, you are simply reassigning 

			Thus
				x === 3
				y === 2

			This is the same as
				let [x, y] = [3, 2]

				and

				var doWork = function(){
					return [1, 3,2];
				}

				let [, x, y] = doWork();
					The comma skips a value
						You can use this at any point in the [] (beg,mid,end, etc)

						Say we didnt have 2 in the doWork function.
							y would be equal to undefined
	Destructuring Objects
		let doWork = function() {
			return {
				firstName: "Zach",
				lastName: "Roofus",
				handles : {
					twitter: "daMan"
				}
			}
		}

		let {
				firstName: first, 
				handles: {twitter: twitterName}
			} = doWork();
				tip: left of the colon is how you navigate into the object
					Ex: firstName: VARIABLE
						handles: twitter: VARIABLE
			

			Thus
			first === "Zach"
			twitterName === "daMan"
				first/twitterName are a let variables

			There is a shortcut (if you're okay using the property names as variable names)

				let {
						firstName, 
						handles: {twitter}
					} = doWork();

				firstName === "Zach"
				twitter === "daMan"

		Utilizing destructuring in parameters
			let doWork = function(url, {data, cache}){
				return data;
			}

			let result = doWork(
				"daMan.com", {
					data: "test",
					cache: false
				}
			);
				Thus
				result === "test"

Default Parameter Values
	let doWork = function(name="Zach", last="Roof", middle="A"){
		return [name, last, middle];
};
	Only works when nothing is passed in OR undefined is passed
		Ex: 
			let [name, last, middle] = doWork("John", undefined)
				name === "John"
				last === "Roof"
				middle === "A"


		Ex2: Works with destructuring as well
			Tip:
				When you see both sides of an assignment have objects/arrays, think destructuring.  As assignments occur in passing in arguments, this is a natural place for destructuring

			let doWork = function(
					url,
					{data = "Scott", cache = true}){
				return data;
			}

			let result = doWork(
				"api/test", {
					cache: false
				}
			);

			result === "Scott"

Rest Parameters
	Utilize this when you can have a variable number of arguments/parameters in a application
		Rest parameter is always the last parameter in a function.  All additional arguments are "sucked up" by this parameter and placed in an array.
			If you don't pass anything into a rest parameter, it will become an empty array.

	Ex:
		let sum = funciton(name, ...numbers) {
			let result = 0;
			numbers.forEach(function(n) {
				result += n; 
			});
			return result;
		}

Spread Operator
	Think about breaking out of an array and then having the individual elements be used in a csv fashion.

	Allows you to pass an array as an argument.  The individual indexes are then passed to the corresponding parameter.

	Ex:
		let doWork = function(x, y, z) {
			return x + y + z;
		}

		var result = doWork(...[1, 2, 3]);

		x === 1
		y === 2
		z === 3

	Allows you to embed arrays
		var a = [4, 5, 6];
		var b = [1, 2, 3, ...a, 7, 8, 9];

		b === [1, 2, 3, 4, 5, 6, 7, 8, 9];

Template Literals
	Allows for a more efficient string concatenation

	Ex:
		let category = "music";
		let id = 2112;
		let id2 = 0;

		let url = `http://apiserver/${category}/${id + id2}`;
			You delimit with back ticks
			The returned value from url will be a string

	You could also use tags to do mutations on the template
	tagged template strings
		I.e. upper `http://apiserver/${category}/${id + id2}`

		You would need to supply your own upper function which needs a certain function signature

		Use cases of tags:
			Help prevent cross-site scripting, localization


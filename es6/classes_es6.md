Classes
	Can be utilized to construct objects
	A Class is an object blueprint
		We instantiate a class when we create an object from the class

	Ex:
		class Employee {
			constructor(name) {
				this._name = name
			}

			doWork() { 	<-Dont need "function" when defining  methods
				return "I can't work anymore bro!";
			}

			getName() {
				return this._name;
			}
		}

			The constructor is a function that is automatically invoked when you utilize the "new" keyword.  "This" inside the object will be equal to the new object that the class creates

			In getName, if you didn't utilize "this", JS wouldn't look in the object for a _name variable, it would look in the getName scope, which is not what you want

		var e = new Employee();

		e.doWork() === "I can't do work anymore bro!"

		This example does exactly what previous versions of JS did (behind the scenes).
		  Behind the scenes in ES6
		  	Employee is being set up as a constructor function
		  	There is a prototype on the construction function that has a doWork method.

	Ex: with getters and setters

		class Employee {
			constructor(name){
				this._name = name;
				// this.name = name;
					Would use this if you wanted to validation logic of the setter, which is what you'd probably want

					As such, this wont be setting a property on the object named "name"
			}

			get name() {
				return this._name
			}
				// Thus you can dynamically create a property

			set name(newValue) {
				if(newValue){
					this._name = newValue;
				}
				
			}

				// Thus you can do validations when setting

		}
			let e1 = new Employee("Zach")
			e1.name === "Zach"
				A getter doesn't take any arguments.

			e1.name = "John"
				Invokation of setter

	Ex: using class inheritance (refactoring the above code)

		class Person {
			constructor(name) {
				this.name = name;
			}

			get name() {
				return this._name;
			}

			set name(netValue) {
				this._name = newValue;
			}
		}

		class Employee extends Person {
			doWork() {
				return `${this._name} is working`;
			}
			// employee "is-a" person
				// Thus we can use inheritance
		}

		Person is a base class for Employee.  In JS, a base class is refered to as a super class
			Now whenever we instantiate an Employee object, it will inherit a _name variable (and any others, etc.) from person and all of the code associated with it

			if we try to create an employee object (and pass in arguments into the class) AND there's no constructor in the employee class, it will call the super classes's constructur and pass in the arguments

			We can extend multiple classes in a class signature.
				CON: This can get very confusing to understand

	Sometimes we need to call a super class from a class.
		super - invokes the same function in the super class and passes in any arguements that IT was passed.

		Ex
			class Employee extends Person {
				constructor(name, title) {
					// how do we set name? It's in the super class
					// We'd do...
						super(name)
					this._title = title
				}
			}
				super(name) will invoke the Person constructor and pass in 'name'




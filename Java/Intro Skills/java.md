Java Software Development Kit (JSDK) aka JDK - If you need to write programs
	Java Runtime Environment (JRE) - Used to run java
		JVM
			An inturpreter that's written for a specific platform (OSX, Windows, etc. )
		Tools - java CLI
		Java Libraries

	To create Java programs
		javac - Java compiler
			Turns your code into byte code that can be run on the JVM

Basic Java programming structure
	The file should reside in a directory that corresponds to the package name.
	The name of the file must be ClassName.java

Semicolins
	Utilize them as a statement terminator

	Dont use semicolins when creating a block of code.
		This code block begins and ends with an curly brace.
			Ex: public class Hello {

				} <-- No semicolin needed

Compiling and Interpreting Applications
	Create the source file
	Compile the source into bytecode
		javac examples/Hello.java
	Interpret the bytecode
		java examples/Hello Bob 
			Bob is a param that's passed in
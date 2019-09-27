---
layout: post
title: Learning C++, the basics #1
date: 2019-09-27 +13:00
published: true
category: Dev
tags: [c++, console app, basic, learning, upskilling, visual studio 2019]
---

The blog side of things has been a little quiet of late. My workplace has been experiencing a downturn in the amount of custom development that's been happening as clients in this region appear to be moving away from custom solutions, to the detriment of my development skills. It's time to rectify that with some upskilling, and what better way to do that than to teach myself some C++?

Time to brush up on the [Google-fu](https://www.urbandictionary.com/define.php?term=google-fu) then huh?


### Starting with the basics

The logical first place to start is *Hello World!* sample of code. Easy enough in theory, but I didn't know how to do it. Quick search told me that I had to use `iostream`, which exposes standard console output, `std::cout`.

```cpp
// file: cpp_primer.cpp

#include <iostream>

int main()
{
  std::cout << "Hello World!";
}
```

Simple enough, but how about a rough C# Console.WriteLine equivalent? Damn, going to need to write a class for that!

Initial attempts were met with syntactic failure - not unexpected, time to find out how to implement a static class - Google to the rescue.

In the process, I found out that each C++ class *can* have a header file, that is treated like a C# interface. Didn't know that... cool!

I created a new class called `Console` and defined the signature within the .h file.

```cpp
// file: Console.h

#ifndef CONSOLE_H
#define CONSOLE_H

#include <string>
#include <iostream>
using namespace std;

#pragma once
class Console
{
public:
	static void Write(string any);
	static void WriteLine(string any);
};

#endif // !CONSOLE_H
```

And went about implementing the class within the .cpp file.

```cpp
// file: Console.cpp

#include "Console.h"

void Console::Write(string any)
{
	cout << any;
}

void Console::WriteLine(string any)
{
	cout << any << endl;
}
```

Basic implementation aside, time to test out this functionality - back to the main function.

```cpp
// file: cpp_primer.cpp

#include <iostream>

int main()
{
  Console::WriteLine("Hello World!"); // I found out that dot notation [x.y()] for invoking functions isn't a thing with static functions in C++, double colon it is [x::y()]
}
```

Hit F5, output is as expected... sweet!


### Getting a little more complicated

The previous examples weren't exactly earth shatteringly complicated, and neither are the next ones, but we're learning, so let's do it incrementally.

Time for a `for` loop.

```cpp
// file: cpp_primer.cpp

#include <iostream>

int main()
{
  // previous code elided
  
  for (int i = 0; i <= dimSize; i++)
	{
    Console::WriteLine("i: " + i);
	}
}
```

Exactly the same as C#...

Let's try printing out a matrix of 2d co-ordinates. Let's make another function, this time within our main file.

```cpp
// file: cpp_primer.cpp

#include "Console.h"
#include <iostream>
#include <string>

using namespace std;

int main()
{
  int matrixSize = 10;
	Print2dMatrix(matrixSize);
}

void Print2dMatrix(int dimSize)
{
	// content elided for brevity, see next code block
}
```

`'Print2dMatrix': identifier not found`

Hmm, this is quite different to C#, slow down slick!

The function is defined within the same class as our `int main()` function, shouldn't I be able to invoke this? Perhaps it needs to be static...

`'Print2dMatrix': identifier not found`

Hmm x2 - I needed a header file for the Console class to be able to invoke static functions before try that!

```cpp
// file: cpp_primer.h

#pragma once

int main();
void Print2dMatrix(int dimSize);
```

Let's implement the changes, don't forget the `#include` statements either!

```cpp
// file: cpp_primer.cpp

#include "Console.h"
#include <iostream>
#include <string>
#include "cpp_primer.h"

using namespace std;

int main()
{
	int matrixSize = 10;
	Print2dMatrix(matrixSize);
}

// prints a 2d array of co-ordinates to the Console, starting at -(dimSize / 2) and ending at +(dimSize / 2)
//		x = dimSize + 1
//		y = dimSize + 1
static void Print2dMatrix(int dimSize)
{
	for (int i = 0; i <= dimSize; i++)
	{
		for (int j = 0; j <= dimSize; j++)
		{
			int manipulatedI = i - ((int)dimSize / 2);
			int manipulatedJ = j - ((int)dimSize / 2);

			string outputString = to_string(manipulatedI) + "," + to_string(manipulatedJ) + "\t";

			Console::Write(outputString);
		}

		cout << endl << endl;
	}
}
```

Right, prints a 2d array of co-ordinates from -5,-5 to 5,5 - that'll do. Is this meant to be hard? (I'm being facetious, I definitely tripped a couple of times).


### How about a bit more on classes?

So, the basics are going well enough.

Time to fall back to my old learning habits; when learning C# and Java a few years back, I'd write the same very basic program every day, adding to the functionality with whatever I learned the previous day. I'd always do C# first, and then Java second, ensuring that I could replicate the same functionality within each language before getting on with the new days' activities. This continued until I had the basics nailed down tight, specifically classes, instantiation, polymorphism, collections etc.

This would always take the shape of a class designed to classify a Person object. Perfect way to have a throwback as well as learn some more C++.

Let's create another class and header file for the Person class. Firstly, the header:

```cpp
// file: Person.h

#ifndef PERSON_H
#define PERSON_H

#include "Console.h"
#include <string>
#include <ctime>

using namespace std;

#pragma once
class Person
{
private:
	string _givenName;
	string _surname;
	string _maidenName;
	int _age;

	Person* _spouse; // tripped up on this - pointers
	tm* _dateOfBirth; // need to put a lot more work into this

public:
	Person(string givenName, string surname, tm* dateOfBirth);

	void SetGivenName(string givenName);
	void SetSurname(string surname);
	void SetDateOfBirth(tm* dateOfBirth);

	string GetGivenName();
	string GetSurname();
	int GetAge();
	tm* GetDateofBirth();

	void GetMarried(Person* spouse);
	Person* GetSpouse();

	string ToString();
};
#endif // !PERSON_H
```

How about an actual implementation:

```cpp
// file: Person.cpp

#include "Person.h"

Person::Person(string givenName, string surname, tm* dateOfBirth)
{
	_givenName = givenName;
	_surname = surname;
	_dateOfBirth = dateOfBirth;
	_age = (time(0) - dateOfBirth->tm_sec);
};

void Person::SetGivenName(string givenName)
{
	_givenName = givenName;
};

void Person::SetSurname(string surname)
{
	_surname = surname;
};

void Person::SetDateOfBirth(tm* dateOfBirth)
{
	_dateOfBirth = dateOfBirth;
	_age = (time(0) - dateOfBirth->tm_sec);
};

string Person::GetGivenName()
{
	return _givenName;
};

string Person::GetSurname()
{
	return _surname;
};

int Person::GetAge()
{
	return _age;
};

tm* Person::GetDateofBirth()
{
	return _dateOfBirth;
};

void Person::GetMarried(Person* spouse)
{
	_spouse = spouse;
	SetSurname(spouse -> GetSurname());
};

Person* Person::GetSpouse()
{
	return _spouse;
};

string Person::ToString()
{
	return _givenName + " " + _surname + " is " + to_string(_age) + " years old"; // age is a large integer as expected, this will form part of another blog post
};
```

Right, so back to our main function for an invocation.

```cpp
#include "Person.h"
#include <iostream>
#include <string>
#include "cpp_primer.h"

using namespace std;

int main()
{
	Person* p1 = new Person("Joe", "Bloggs", new tm());
	Person* p2 = new Person("Jane", "Doe", new tm());

	Console::WriteLine(p1->ToString());
  // output = Joe Bloggs is 1569559283 years old
  
	Console::WriteLine(p2->ToString());
  // output = Jane Doe is 1569559283 years old

	p2->GetMarried(p1);

	Console::WriteLine(p2->ToString());
  // output = Jane Bloggs is 1569559283 years old
  
  // other implementations elided
}
```

Looks pretty straight forward, simply instantiates two people, outputs their details, marries one to the other and changes the surname before outputting the changed details. Man oh man, did I trip up hard on this though... pointers!


### What the hell is a pointer?

You'll see in the above code that I've implemented pointers for my `Person` class to be able to reference other `Person` classes. That took a while to figure out, but in a nutshell what I've come to understand is that there are two ways to be able to assign an instance member in C++

- by reference
- by pointer

Referencing, from my limited understanding assigns a copy of the primitive/object/whathaveyou to the member.

Pointers actually point the member to the exact object, meaning that there is only one of them, instead of multiple.

I don't know if my understanding of pointers is correct yet, but that's what I've come to understand so far.

I'll take a deeper dive in a future post.

Cheers for reading...

---
layout: post
title: __proto__ vs prototype in JavaScript
categories: Tech-Post JavaScript
description: The difference between __proto__ vs prototype in JavaScript
keywords: JavaScript __proto__ prototype object
---

![proto_vs_prototype](/images/blog/_proto_vs_prototype.jpeg)


## Why we need to know prototype?

JavaScript is dynamic proramming language, it does now provide class like Java or Python. The class keyword is introduced in ES6, but is syntactical sugar, JavaScript remains prototype-based.

Prototype is a must known topic in JavaScript and it is the foundation of JavaScript class especially when we talking about the inheritance. Prototype may not appear often in the work but it is everywhere in the package, open sourced library and frameworks. 


## \__ proto__ vs prototype

Each class has prototype and each instance has \__ proto__,the prototype of class is equal to \__ proto__ of instance that create from same class.

### Example of inheritance
We create the people class and the student class that inherit from people class.

```javascript
class People {
	constructor(name){
  	this.name = name
	}
}

class Student extends People {
	constructor(name,major){
		super(name)
    this.major = major
  }
  study(){
  	console.log(`${this.name} is studing ${this.major}`)
  }
}
```

Now let us find what the prototype of Student class
```javascript
console.log(Student.__proto__)
/*
People {constructor: ƒ, study: ƒ}
constructor: class Student
study: ƒ study()
__proto__: Object
*/
```

And what the prototype of Student class
```javascript
let john = new Student("John", "Computer Science")
console.log(john.prototype)
/*
People {constructor: ƒ, study: ƒ}
constructor: class Student
study: ƒ study()
__proto__: Object
*/
```

let compare the Student.prototype and john.\__ proto__

```javascript
console.log(Student.prototype===john.__proto__)//true
```

Yes they are equal, so the prototype of a class is equal to the \__ proto__ from instance that created from same class. 

Also, the prototype of class or \__ proto__ of instance is an object from their parent class. This means if you want to find prototype of their parent class, you can try three different ways.

```javascript
console.log(People.prototype)
console.log(Student.prototype.__proto__)
console.log(john.__proto__.__proto__)
```

If you print out the result from above, it is like below
```
{constructor: ƒ}
constructor: class People
__proto__: Object
```

This means the final prototype of the class will point to the Object class. The Object class is like a world builder. It is the top and final class. This class give us the whole world of the javascript. The prorotype from final class is null as it does not have parent class.

## Prototype Chain
Now we should know what is prototpye chain.  Each object has a private property which holds a link to another object called its prototype. That prototype object has a prototype of its own, and so on until an object is reached with null as its prototype. Those prototypes that help connect object to other object is a chain of prototype, we called prototype chain.
---
layout: post
title: Swift enums - Part 1
---

In his simplest form, Swift enums are just the same as we are used to in Objective C.

{% highlight swift %}
	enum Level {
		case Beginner
		case Apprentice
		case Master
		case Superhero
	}
{% endhighlight %}


But Swift introduces us to new possibilities that makes enums serious and powerfull citizens for our job.
Now we can have associated values, we can nest enums, we can add enums to structs (as well as classes, of course), we can use tuples as values, we can add static and mutating methods and properties in enums. Enums can conform to protocols, can be extended, can have inits and can use generics.

So let's see all the new features step by step. Let's start with associated values,

## Associated values

We can use four types of values for enums: Integer, Floating point, String amd Boolean

We can make enum values explicit

{% highlight swift %}
	enum Level: Int {
		case Beginner = 1
		case Apprentice = 2
		case Master = 3
		case Superhero = 4
	}
{% endhighlight %}

{% highlight swift %}
	enum Faction: String {
		case Villain = "The bad boys"
		case Hero = "The good ones"
	}
{% endhighlight %}

but we can also make them implicit

{% highlight swift %}
	enum Level: Int {
		case Beginner = 1, Apprentice, Master, Superhero
	}
{% endhighlight %}

> Beginner = 1, Apprentice = 2, Master = 3, Superhero = 4

{% highlight swift %}
	enum Faction: String {
		case Villain
		case Hero
	}
{% endhighlight %}

> Villain = "Villain", Hero = "Hero"

The use for any of these enums will be like,

{% highlight swift %}
	let supermanLevel = Level.Superhero.rawValue
	let supermanType = Faction.Hero.rawValue
{% endhighlight %}

Keep in mind that accessing rawValue will return an optional, as the rawValue may not exist. That may happen if we have,

{% highlight swift %}
	let supermanLevel = Level(rawValue: 15)
{% endhighlight %}

Will see how to access values in a cleaner way later.

As you can guess, having enums defined like above are not very useful for creating usefull heroes. So make use of the nested enums to fix it.

## Nested enums

Let's rewrite our code:

{% highlight swift %}
	enum Character {

		case Villain
		case Hero
		
		enum Superpower: String {
			case Invisibility
			case Fly
			case Fireball
			case Mutant
		}

		enum Level: Int {
			case Beginner = 1
			case Apprentice = 2
			case Master = 3
			case Superhero = 4
		}
	}
{% endhighlight %}

Now we can use it like this,

{% highlight swift %}
	let superman = Character.Hero
	let supermanLevel = Character.Level.Superhero //This will be Int 4
	let supermanPower = Character.Superpower.Mutant //This will be String "Mutant"
{% endhighlight %}

We can also use our enum as a func param,

{% highlight swift %}
	func requestHelp(to helper: Character) {
		if (helper == .Hero) {
			print "Stay calmed"
		} else {
			print "Something bad will happens"
		}
	}

	requestHelp(to:superman)

{% endhighlight %}

This is a little cleaner, but we can do better.

## Enums in structs

One step further, let's put our enum inside a struct so we can have a really usefull object.

{% highlight swift %}
	struct Character {
		enum CharacterType {
			case Villain
			case Hero
		}

		enum Superpower: String {
			case Invisibility
			case Fly
			case Fireball
			case Mutant
		}

		enum Level: Int {
			case Beginner = 1
			case Apprentice = 2
			case Master = 3
			case Superhero = 4
		}

		let type: CharacterType
		let power: Superpower
		let level: Level
	}
{% endhighlight %}

And use it,
(remember that Swift structs provide us with a default init if we do not write one)

{% highlight swift %}
	let superman = Character(type: .Hero, power: .Mutant, level: .Superhero)
{% endhighlight %}

That's the first part of our Swift enum series. See you soon !

[Playground link](https://github.com/ShootrNetwork/blog-examples/tree/master/ios)

Christian Cabarrocas

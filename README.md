# General Software Design Principles
There are a number of principles of software design that are useful to keep in mind when writing code. These same principles should be considered during Code Reviews and Pull Requests. Following these principles will make your code easier to read, easier to write, and easier to maintain.

## DRY Principle
DRY stands for "Don't Repeat Yourself." If you find yourself copying and pasting code, then it's a good idea to move that copied code into its own method/class and reference it, instead.

Bad Example:
```
public void StopHittingYourself()
{
	Console.WrtieLine("Bob: \"Ow!\"");
	Console.WriteLine("Tom: \"Stop Hitting Yourself!\"");
	Console.WrtieLine("Bob: \"Ow!\"");
	Console.WriteLine("Tom: \"Stop Hitting Yourself!\"");
	Console.WrtieLine("Bob: \"Ow!\"");
	Console.WriteLine("Tom: \"Stop Hitting Yourself!\"");
	Console.WrtieLine("Bob: \"Ow!\"");
	Console.WriteLine("Tom: \"Stop Hitting Yourself!\"");
	Console.WrtieLine("Bob: \"Ow!\"");
	Console.WriteLine("Tom: \"Stop Hitting Yourself!\"");
	Console.WrtieLine("Bob: \"Ow!\"");
	Console.WriteLine("Tom: \"Stop Hitting Yourself!\"");
}
```
Good Example:
```
public void StopHittingYourself()
{
	SayStopHittingYourself();
	SayStopHittingYourself();
	SayStopHittingYourself();
	SayStopHittingYourself();
	SayStopHittingYourself();
	SayStopHittingYourself();
}

private void SayStopHittingYourself()
{
	Console.WrtieLine("Bob: \"Ow!\"");
	Console.WriteLine("Tom: \"Stop Hitting Yourself!\"");
}
```
Better Example:
```
public void StopHittingYourself()
{
	for(var i = 0; i < 6; i++) //Loops are your friend.
	{
		SayStopHittingYourself();
	}
}

private void SayStopHittingYourself()
{
	Console.WrtieLine("Bob: \"Ow!\"");
	Console.WriteLine("Tom: \"Stop Hitting Yourself!\"");
}
```

## KISS Principle
Arguably the first design principle coined, the KISS principle is credited to Kelly Johnson of Lockheed Skunk Works, and dates back to 1960. KISS stands for "Keep It Simple, Stupid" (alternatives include "Keep It Stupid-Simple" and "Keep It Short and Simple") and was used to encourage Lockheed's designers to make products that were so simple that they could be repaired in the middle of a battle by a poorly-trained tech. In software development, the rule is very much the same. It is not unusual for code to pass through multiple pairs of hands and be maintained and modified by people of all skill levels. Good code should be simple enough and obvious enough that a Junior Dev on their first day can figure out what it's supposed to do. Fancy design patterns and heavily normalized code might look cool and impress your teammates, but if it's too complicated, then it's useless in the long term.

## Authored Code Principle
The Authored Code Principle dictates that code should be able to be read like a good story. Any person, including a non-dev, who looks at the code should be able to get an understanding of what that code does. 

 - ### Beginning, Middle, and End
   Authored code has a flow. A reader, reading a class from top to bottom, should be able to follow the flow of the code as if it was a story. The first method in a class should be the very first thing that happens. Other, internal (private) methods should follow in the order that they are used.

 - ### Obvious Names
   Names of methods, properties, classes, etc should be both descriptive and obvious. A reader, even one with no technical experience, should be able to clearly understand what the method is doing or what the variable represents.

 - ### Avoid Abbreviations
   It is best to use short, clear names. When that's not possible, prefer long names over abbreviations.
   
 - ### When in doubt, write it out
   Avoid complex ternaries and other tricks to make your code small. Compact code may look pretty, but it can be very difficult to read and understand.

 - ### Avoid Comments
   Comments should only be used when something is not obvious. For example, a method named "GetCurrentTime" doesn't need a comment to indicate that it gets the current time. However, if the method GetCurrentTime is (for example) saving that time to a database or applying it to a model, then a comment would be needed to understand that. A better solution (in this case, and most others) would be to refactor the method to have a single responsibility or give it a more obvious name.

```
namespace StoryTime
{
//An obvious name that makes the purpose of this class very obvious.
	public class StoryWriter
	{
//Class has a single responsibility - It does one thing and it does it completely
		public Story WriteStory(string characterName) 
		{
			var story = new Story();
			story.Beginning = Begin(characterName);
			story.Middle = HerosJourney(story);
			story.End = End(story);
			return story;
		}
		
//Private methods listed in order used, with obvious names.
		private Beginning Begin(string characterName)
		{
			var character = IntroduceCharacter(characterName);
			var setting = IntroduceLocation(character);
			var mentor = CreateMentor(character, setting);
			return new Beginning(character, setting, mentor);
		}
		
		private Middle HerosJourney(Story theStorySoFar){...}
		private Ending End(Story theStorySoFar){...}
		
		private Character IntroduceCharacter(string characterName){...}
		private Setting IntroduceLocation(Character character){...}
		private Mentor IntroduceMentor(Character character, Setting setting){...}
		
//It's okay to have long names - NEVER use abbreviations
		private Trials CreateProblemThatCharacterNeedsToSolve(){...}
	}
}
```

## SOLID
SOLID principles are a set of best practices for Object-Oriented Programming. Generally considered to be an industry standard, SOLID principles lay out some of the most vital expectations for a developer to follow when writing code. Deviation from SOLID principles result in code that is very difficult to maintain.

 - ### Single Responsibility Principle
   Every piece of code should do exactly one thing, and it should do that one thing completely. This applies to every structure in code, regardless of its place in the codebase. In other words, every method should have a single responsibility, every class should have a single responsibility, and every application should have a single responsibility.

   Bad example:
   ```
	//This function has seven lines and performs six different actions.
	public Cake MixIngredientsAndBakeCake(){
		var uncookedCake = GetCakeMix();
		var stirredIngredients = StirIngredients(uncookedCake);
		var pan = GetPan();
		var greasedPan = GreasePan(pan);
		var unbakedCake = PutMixInPan(stirredIngredients, greasedPan);
		var bakedCake = Bake(unbakedCake);
		return bakedCake;
	}
   ```
   Good Example:
   ```
	//It can easily be broken down into three Single Responsibility functions:
	public Cake BakeCake() {
		var cakePan = PrepPan();
		cake.AddIngredients(MixIngredients());
		return Bake(cakePan);
	}

	private Ingredients MixIngredients (){
		var uncookedCake = getCakeMix();
		return stirIngredients(uncookedCake);
	}

	private Pan PrepPan() {
		var pan = getPan();
		return greasePan(pan);
	}
   ```
   In the above example, if a change needs to be made to the MixIngredients method, it won't affect the rest of the class. In this way, small changes and improvements can be made without requiring major refactoring to the rest of the application. Further, a person who needs to make a change but has never seen the code would know exactly where to make the change, and not have to worry about what else the change might affect.

 - ### Open-Closed Principle
   The Open-Closed Principle states that a class should be open to extension, but closed to modification. In other words, since a class has a single responsibility, we can rely on that class to do that one thing and do it well. If we need it to do something else, we can create another class that extends that functionality. To address a misnomer in this principle, extension in this context is not related to C# extension classes or the JavaScript "extends" keyword, though both of these may be used to extend the functionality of a class. Composition is preferred over inheritance in nearly all situations - That is, it is better to use another Single Responsibility class that contains an _instance_ of the original class alongside the additional functionality, than it is to extend the original class through abstraction.

   Bad Example (using the same class as earlier):
   ```
	public class CakeMaker
	{
		public Cake BakeCake() {
			var cakePan = PrepPan();
			cakePan.AddIngredients(MixIngredients());
			var cake = Bake(cakePan);
			var decoratedCake = DecorateCake(cake);//Added logic to original class
			return decoratedCake;		
		}

		private Ingredients MixIngredients (){...}

		private Pan PrepPan() {...}
		
		private Cake DecorateCake() {...} //Added method to original class
	}
   ```
   Better Example (Inheritence):
   ```
	public class DecoratedCakeMaker : CakeMaker
	{
		public Cake BakeDecoratedCake()
		{
			var cake = base.BakeCake();
			return DecorateCake(cake);
		}
		private Cake DecorateCake(Cake cake) {...}
	}
   ```
   Best example:
   ```
	public class DecoratedCakeMaker
	{
		public Cake DecorateCake(Cake cake) {...}
	}
   ```

 - ### Liskov Substitution Principle
   Any class that implements an interface should have all of the functionality expected of that interface. Given that, any class that _calls_ such an implementation should get the results in the format and type that it expects. If one were to substitute a completely different implementation, the calling class should not need to change.

   Example:
   ```
	//Given an interface
	public interface IAmAnInterface
	{
		bool AmIClassy();
	}
	
	//Then anything that calls that interface should know what to expect
	public class CallingClass
	{
		private readonly IAmAnInterface _implementation;
		public ClassyClass(IAmAnInterface implementation)
		{
			_implementation = implementation;
		}
		
		public bool IsClassy()
		{
			return _implementation.AmIClassy();
		}
	}
	
	//Whether the implementation is this:
	public class AllTheClass : IAmAnInterface
	{
		public bool AmIClassy() => true;
	}
	
	//Or this:
	public class NoClass : IAmAnInterface
	{
		public bool AmIClassy() => false;
	}
	
	//The CallingClass doesn't care.
   ```
   
 - ### Interface Segregation Principle
   This is basically the Single Responsibility Principle as it relates to interfaces. A class should never have to implement methods or properties that it doesn't need, so interfaces should limit their signatures to only those methods or properties that it requires.
   
   Bad Example
   ```
	//This interface has several properties that may be relevant, but may not be, and will probably not always be used.
	public interface IEatPaste
	{
		int Age { get; set;}
		dobule MaturityLevel { get; set;}
		string PasteBrand { get; set;}
		bool IsPasteTasty();
		bool IsThereAnyPasteLeft();
		bool DoIHaveASpoon();
	}
   ```
   Good Example
   ```
	//By breaking down the interfaces to their Single Responsibility, they can be used independantly or together, without requiring any unwanted logic.
	public interface IAmPaste
	{
		string PasteBrand { get; set;}
		bool IsThereAnyPasteLeft();
	}
	
	public interface ILikePaste
	{
		bool IsPasteTasty();
	}
	
	public interface IAmChildLike
	{
		int Age { get; set;}
		dobule MaturityLevel { get; set;}
	}
	
	public interface IEatPaste : IAmPaste, ILikePaste, IAmChildLike
	{
		bool DoIHaveASpoon();
	}
   ```
   
 - ### Dependency Inversion Principle
   The Dependency Inversion Principle, also known as Inversion of Control dictates that modules (or packages, or shared projects, or classes) should not have dependencies on concrete classes. Instead, they should depend on interfaces, so that if the logic in the concrete class changes, the modules don't need to be rewritten. (This is a very simplified explanation, more information can be found [here](https://en.wikipedia.org/wiki/Dependency_inversion_principle).) In this way, the dependency is "inverted" - the module no longer depends on the concrete class, but both the module and the concrete class depend on the interface. This is generally implemented with "Dependency Injection", where interfaces are registered in a single location with their various implementations, and if a class needs to access one, it asks the registration system for the correct implementation. Dependency injection simplifies modification and makes code easier to maintain. Common Dependency Injection platforms include AutoFac and Ninject.
   
   Bad Example:
   ```
	public class LogicalStuff {...}
	
	public class SomeApiThatUsesLogicalStuff
	{
		private LogicalStuff _logicalStuff = new LogicalStuff();

		//This API now DEPENDS on LogicalStuff.
		//We can't move LogicalStuff to a different project without 
		//   adding a reference (dependency) to that project.
		//If we need to substitute LogicalStuff with DifferentLogicalStuff,
		//   it'll need to be changed everywhere it's been instantiated.
		//And this is REALLY hard to test.
	}
   ```
   Good Example:
   ```
	public interface ILogicalStuff {...}
	
	//LogicalStuff now implements (depends on) ILogicalStuff
	public class LogicalStuff : ILogicalStuff {...}
	
	public class SomeApiThatUsesLogicalStuff
	{
		private readonly ILogicalStuff _logicalStuff;

		public SomeApiThatUsesLogicalStuff(ILogicalStuff logicalStuff)
		{
			_logicalStuff = logicalStuff;

			//Now the Api depends on the interface, but has no dependency on LogicalStuff.
			//An implementation (registered with the IOC system) is injected into the api.
			//The LogicalStuff class is no longer "Tightly coupled", so it can be
			//   moved, modified, versioned, or even completely replaced without changing
			//   the Api.
			//And we can inject a Mock class (a fake implementation) to help with testing.
		}
	}
   ```
   

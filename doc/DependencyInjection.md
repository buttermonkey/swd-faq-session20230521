# Dependency Injection

Dependency injection is all about avoiding hard dependencies in your code, so that it will be easy to change.

As an example, consider we have an application, that should perform some statistical analysis on a population of people:

```java
public class PopulationAnalyzer {
	public void analyze() {
		IPopulationProvider population = new PopulationDatabase("jdbc:postgresql://localhost:5432/austria");
		// crunch numbers
	}
}

public class App {
	public static void main(String[] args) {
		PopulationAnalyzer analyzer = new PopulationAnalyzer();
		analyzer.analyze();
	}
}
```

While this is functioning code, there is a huge problem with it: it is impossible to test it, because we cannot simply
use a test database instead of the production database. Also, how do we analyze a different population without changing
the code of our application analyzer?

So - I can you hear - hardwiring dependencies is a bad idea. What can we do about it?

And given by the titel of this chapter I would also guess you know the answer already: we are passing them down to our
number crunching code:

```java
public class PopulationAnalyzer {
	public void analyze(IPopulationProvider population) {
		// crunch numbers
	}
}

public class App {
	public static void main(String[] args) {
		IPopulationProvider population = new PopulationDatabase("jdbc:postgresql://localhost:5432/austria");
		PopulationAnalyzer analyzer = new PopulationAnalyzer();
		analyzer.analyze(population);
	}
}
```

Well, this works. But what if our population analyzer has to implement an interface that didn't foresee the existence
of our IPopulationProvider interface? Easy: just make it a private field of the population analyzer and pass it in via
constructor:

```java
public interface IAnalyzer {
	void analyze();
}

public class PopulationAnalyzer implements IAnalyzer {
	private final IPopulationProvider population;
	
	PopulationAnalyzer(IPopulationProvider population) {
		this.population = population;
    }
	
	@Override
	public void analyze() {
		// crunch numbers
	}
}

public class App {
	public static void main(String[] args) {
		IPopulationProvider population = new PopulationDatabase("jdbc:postgresql://localhost:5432/austria");
		PopulationAnalyzer analyzer = new PopulationAnalyzer(population);
		analyzer.analyze();
	}
}
```

Well, and that's (almost) all there is to dependency injection :-)

# Consider a builder when faced with many constructor parameters


**Item 2: Consider a builder when faced with many constructor parameters.**


In the previous item, we discussed how static factory methods should be a preferred choice over constructors. While the above method works in most cases, in cases where there are a bunch of optional parameters it does not scale well.

The book consist of an example of a class that represents a Nutrition Facts label. This class would have a bunch of fields. Many of them would be required, and many would be optional. To create an instance of such a class, you have a few options.

***Use the telescoping constructor pattern.*** 

This pattern would have a constructor with all the required parameters. Adding to that, it would have constructors with the required parameters with one optional parameter, 2 optional parameters, 3 optional parameters and so on. Here is an example given in the book.
```
public class NutritionFacts {
	private final int servingSize;       //required
	private final int servings;          //required
	private final int calories;          //optional
	private final int fat;               //optional
	private final int carbohydrates;     //optional

	public NutritionFacts(int servingSize, int servings) {
		this.servingSize = servingSize;
		this.servings = servings;
	}

	public NutritionFacts(int servingSize, int servings, int calories) {
		this.servingSize = servingSize;
		this.servings = servings;
		this.calories = calories;
	}

	public NutritionFacts(int servingSize, int servings, int calories, int fat) {
		this.servingSize = servingSize;
		this.servings = servings;
		this.calories = calories;
		this.fat = fat;
	}

	public NutritionFacts(int servingSize, int servings, int calories, int fat, int carbohydrates) {
		this.servingSize = servingSize;
		this.servings = servings;
		this.calories = calories;
		this.fat = fat;
		this.carbohydrates = carbohydrates;
	}

}
```

- With the above pattern, often you still need to pass in null/zero values for parameters that you are not interested in.
- The other problem with the above method is that it is not readable. The client of the above class would need to know exactly the order of the parameters in the constructor and hence prone to runtime errors.

***Use the java beans pattern.***

IUn this pattern, you create an instance using a non-parameterized constructor and then use setter methods to set values for fields you are interested in. Here is an example:

```
public class NutritionFacts {
	private final int servingSize = -1;       //required
	private final int servings = -1;          //required
	private final int calories = 0;           //optional
	private final int fat = 0;                //optional
	private final int carbohydrates = 0;      //optional


	public NutritionFacts() {

	}

	// setter methods for the above fields.
}

// Client use
NutritionFacts facts = new NutritonFacts();
facts.setServingSize(10);
facts.setServings(1);
facts.setFat(10);
```

The above pattern has flaws.

- As the object is created by invoking the constructor and then the fields are set subsequently across multiple statements, the object may be in an inconsistent state during its construction.
- In usual cases, the constructor would be responsible for checking the validity of all parameters and create an instance only if the parameters are valid unlike above. 
- We also cannot use the above pattern in cases of immutable classes.
- As the construction of the object spans across multiple statements, thread safety is a concern in multi-threaded environments.

***Use the builder pattern.***

This is considered as a best way to create objects which has a lot of optional parameters. Here we introduce a `builder class` as a static nested class in the main class(candidate class). This builder class exposes the same setter methods for the fields that are optional in the parent class along with a constructor with the required parameters. The client can create an instance of the builder class and then call the `build` method on the builder object to create an instance of the parent(main) class. Here is an example:
```
public class NutritionFacts {
	private final int servingSize;       //required
	private final int servings;          //required
	private final int calories;          //optional
	private final int fat;               //optional
	private final int carbohydrates;     //optional


	public static class Builder {
		private final int servingSize;       //required
		private final int servings;          //required

		private final int calories = 0;          //optional
		private final int fat = 0;               //optional
		private final int carbohydrates = 0;     //optional

		public Builder(int servingSize, int servings) {
			this.servingSize = servingSize;
			this.servings = servings;
		}

		public NutritionFacts build() {
			return new NutritionFacts(this);
		}

		// setter methods for the optional fields. Should return this for method chaining.
	}

	public NutritionFacts(Builder builder) {
		this.servingSize = builder.servingSize;
		this.servings = builder.servings;
		this.calories = builder.calories;
		this.fat = builder.fat;
		this.carbohydrates = builder.carbohydrates;
	}

}
```
**Advantages**

- The above method makes NutritionalFacts class immutable. Hence avoiding the thread safety concern present in the java beans pattern.
- A single builder object can be used to create multiple parent objects.
- A constructor can have a single varargs parameter. Because a builder uses setter methods for optional parameters, it can have multiple varargs parameters.

**Disadvantages**

- In order to create an object, you first need to create a builder object. This may cause a performance hit if the object is huge.
- The builder pattern is quite verbose.

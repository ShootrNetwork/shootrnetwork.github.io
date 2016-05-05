---
layout: post
title: Transformation Priority Premise
---

Transformation Priority Premise (TPP) is a programming approach developed by Robert Martin (aka **Uncle Bob**) as a refinement to make the process of test-driven development (TDD) easier and more effective for a computer programmer. So, first of all, let’s remember the three TDD rules:

* You are not allowed to write any production code unless it is to make a failing unit test pass.

* You are not allowed to write any more of a unit test than is sufficient to fail; and compilation failures are failures.

* You are not allowed to write any more production code than is sufficient to pass the one failing unit test.

This post is about examples of Transformation Priority premises. When I first read it I was a little bit shocked, so I thought that writing a post could be helpful to understand this.

The transformations list guides on **how to apply small transformations to the code under test in order to evolve it to a more generic implementation**. We should avoid taking big steps forward when we have the chance of making a small transformation.

**Transformations on the top of the list should be preferred to those that are lower**. It is better (or simpler) to change a constant into a variable than it is to add an if statement. So when making a test pass, you try to do so with transformations that are simpler (higher on the list) than those that are more complex. REMEBER: **YOU SHOULD EVOLVE YOUR CODE TAKING BABY STEPS**.

1. ({} -> nil) no code at all->code that employs nil

2. (nil -> constant)

3. (constant -> constant+) a simple constant to a more complex constant

4. (constant -> scalar) replacing a constant with a variable or an argument

5. (statement -> statements) adding more unconditional statements.

6. (unconditional -> if) splitting the execution path

7. (scalar -> array)

8. (array -> container)

9. (statement -> recursion)

10. (if -> while)

11. (expression -> function) replacing an expression with a function or algorithm

12. (variable -> assignment) replacing the value of a variable.

Let’s begin with the examples:

**({} -> nil) no code at all->code that employs nil**

{% highlight c# %}
public class RomanConverter {
   public string Convert(int number) {
       return null;
   }
}    
{% endhighlight %}

**(nil -> constant)**

{% highlight c# %}
public class RomanConverter {
   public string Convert(int number) {
       return “I”;
   }    
}
{% endhighlight %}

**(constant -> scalar) replacing a constant with a variable or an argument**

{% highlight c# %}
public class RomanConverter {
   public string Convert(int number) {
       var result = “I”;
       return result;
   }
}
{% endhighlight %}

**(statement -> statements) adding more unconditional statements.**

{% highlight c# %}
public class RomanConverter {
   public string Convert(int number) {
       var result = “I”;
       result += “I”;
       return result;
   }
}
{% endhighlight %}

**(unconditional -> if) splitting the execution path**

{% highlight c# %}
public class RomanConverter {
   public string Convert(int number) {
       var result = “I”;
       if (number >= 1) {
           result += “I”;
       }
       return result;
   }
}
{% endhighlight %}

**(scalar -> array)**

{% highlight c# %}
public class RomanConverter {
   public static readonly string[] Results = { “I”, “II”, “III” };
   public string Convert(int number) {
       return Results[number - 1];
   }
}
{% endhighlight %}

**(array -> container) + (constant -> constant+) a simple constant to a more complex constant**

{% highlight c# %}
public class RomanConverter {
   public static readonly IDictionary<int, string> Results =
       new Dictionary<int, string> {
           {1, “I”},
           {2, “II”},
           {3, “III”},
           {4, “IV”},
       };
   public string Convert(int number) {
       return Results[number];
   }
}
{% endhighlight %}

**(statement -> recursion)**

{% highlight c# %}
public class RomanConverter {
   public static readonly IDictionary<int, string> Results = new Dictionary<int, string> {
       {1, “I”},
       {4, “IV”},
   };
   public string Convert(int number) {
       if (Results.ContainsKey(number)) {
           return Results[number];
       }
       return Results[1] + Convert(number - 1);
   }
}
{% endhighlight %}

**(if -> while)**

You should go from this:

{% highlight c# %}
public class RomanConverterShould {
   [TestCase(1, “I”)]
   [TestCase(2, “II”)]
   [TestCase(3, “III”)]
   [TestCase(4, “IV”)]
   [TestCase(5, “V”)]
   [TestCase(6, “VI”)]
   [TestCase(7, “VII”)]
   [TestCase(8, “VIII”)]
   [TestCase(9, “IX”)]
   [TestCase(10, “X”)]
   [TestCase(40, “XL”)]
   [TestCase(44, “XLIV”)]
   public void ConvertNumberToRoman(int number, string expected) {
       var romanNumeral = new RomanConverter().Convert(number);
       Assert.That(romanNumeral, Is.EqualTo(expected));
   }
}
public class RomanConverter {
   public static readonly IDictionary<int, string> Results =
       new Dictionary<int, string> {
           {1, “I”},
           {4, “IV”},
           {5, “V”},
           {9, “IX”},
           {10, “X”},
           {40, “XL”},
       };
   public string Convert(int number) {
       if (Results.ContainsKey(number)) {
           return Results[number];
       }
       if (number > 40) {
           const string result = “XL”;
           return result + Convert(number - 40);
       }
       if (number > 10) {
           const string result = “X”;
           return result + Convert(number - 10);
       }
       if (number > 5) {
           const string result = “V”;
           return result + Convert(number - 5);
       }
       return Results[1] + Convert(number - 1);
   }
}
{% endhighlight %}

to this:

{% highlight c# %}
public class RomanConverter {
   public static readonly IDictionary<int, string> mappings =
       new Dictionary<int, string> {
           {40, “XL”},
           {10, “X”},
           {9, “IX”},
           {5, “V”},
           {4, “IV”},
           {1, “I”},
       };
   public string Convert(int number) {
       string result = string.Empty;
       var mappingsEnumerator = mappings.GetEnumerator();
       while (mappingsEnumerator.MoveNext()) {
           var mapping = mappingsEnumerator.Current;
           while (number >= mapping.Key) {
               result += mapping.Value;
               number -= mapping.Key;
           }
       }
       return result;
   }
}
{% endhighlight %}

**(expression -> function) replacing an expression with a function or algorithm**

from:

{% highlight c# %}
public class RomanConverter {
   public string Convert(int number) {
       var result = “I”;
       if (number > 1) {
           result += “I”;
       }
       if (number > 2) {
           result += “I”;
       }
       return result;
   }
}
{% endhighlight %}


to:

{% highlight c# %}
public class RomanConverter {
   public string Convert(int number) {
       var result = “I”;
       return TransformNumber(result, number);
   }
   private string TransformNumber(int result, int number) {
       if (number > 1) {
           result += “I”;
       }
       if (number > 2) {
           result += “I”;
       }
       return result;
   }
}
{% endhighlight %}

Alright, silly example. But I’m not lying.

**(variable -> assignment) replacing the value of a variable.**

{% highlight c# %}
var result = “I”;
if (number >= 1) {
    result += “I”;
}
{% endhighlight %}

**Author**: [Arturo Jiménez](https://github.com/artjimlop)

**Links**

[8thlight](http://blog.8thlight.com/uncle-bob/2013/05/27/TheTransformationPriorityPremise.html)

[Wikipedia](https://en.wikipedia.org/wiki/Transformation_Priority_Premise)

[Codurance](http://codurance.com/2015/05/18/applying-transformation-priority-premise-to-roman-numerals-kata/)


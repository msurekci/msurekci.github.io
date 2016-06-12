---
layout: post
title:  "Roman Numerals to Arabic numbers conversion kata - Part 1 - Happy case"
date:   2016-05-27
author: Mahmut Surekci
categories: software development, c#, roman numerals, arabic numbers, numbers, kata, tdd
---

A common coding exercise for developers to practice their skills is the Roman Numerals to Arabic numbers conversion kata. I am going to go through this kata doing TDD.

For this kata I will be using NUnit and C#. The code for this exercise is available here:
[Roman Numerals tests]("https://github.com/msurekci/Exercises/blob/master/Exercises/UnitTest/RomanNumerals/RomanNumeralsToNumbersTests.cs") 
[Roman Numerals implementation]("https://github.com/msurekci/Exercises/blob/master/Exercises/Exercises/RomanNumerals/RomanNumeralConverter.cs")

Our first test will be to convert the roman numeral "I" to 1.

```csharp
[TestCase("I", 1)]
public void RomanNumeralShouldBeConvertedToCorrectArabicNumbers(string romanNumeral, int expectedArabicNumber)
{
    var result = new RomanNumeralConverter().Convert(romanNumeral);

    Assert.AreEqual(expectedArabicNumber, result);
}
```

Now we have our failing test. We can now implement the solution:

```csharp
public class RomanNumeralConverter
{
    public int Convert(string romanNumeral){
        return 1;
    }
}
```

We should be all green now. From now on we can continue adding new tests by just adding a new test case each time:

```csharp
[TestCase("II", 2)]
```

We now require to return 2 when we are passed in "II".

```csharp
public int Convert(string romanNumeral){
    if(romanNumeral == "II"){
        return 2;
    }

    return 1;
}
```

This is probably one of the easiest ways to make that particular test pass. But, we know that the first three roman numerals just keep adding I's so maybe we can do something smarter.

```csharp
int result = 0; 

for(int i = 0; i < romanNumeral.Length; i++){
    if(romanNumeral[i] == 'I'){
        result += 1;
    }
}

return result;
```

What we are doing is iterating through each character (notice that it is a char and not a string comparison) and if the character eqauls and I then we add 1 to the result which we then return. We have a working solution up to 3.

Our next failing test will be:

```csharp
[TestCase("IV", 4)]
```

The simplest possible way to make the test pass would be to put the if statement and have a special case in there for it. 

```csharp
if(romanNumeral == "IV"){
    return 4;
}
```

We'll leave it like that for now to help us spot a pattern. 

Next let's do "V":

```csharp
[TestCase("V", 5)]
```

Once again we have a new thing here. Let's once again make it pass with a simple if.

```csharp
if(romanNumeral == "V"){
    return 5;
}
```

The next scenario is "VI":

```csharp
[TestCase("VI", 6)]
```

Here we are starting to see a pattern form. When the I is before the V it is reducing 1 and when it is after it is adding 1 to the result. Maybe we can get rid of those if statements now.

First step is to create a Dictionary with the roman numerals and their corresponding values.

```csharp
readonly Dictionary<char, int> romanNumerals = new Dictionary<char, int> { { 'I', 1 }, { 'V', 5 }, { 'X', 10 }, { 'L', 50}, { 'C', 100 }, { 'D', 500}, { 'M', 1000 } }; 
```

We now need to iterate over this list, then iterate over each character in the roman numeral. If it matches the character we are checking in our dictionary then we check if there is another character after it. If there is we must
then check if it is greater than the one we have. If so, then we can subtract it otherwise we will add it on.

```csharp
int result = 0;

var remainingRomanNumeral = new StringBuilder(romanNumeral);

int i;
    
for (i = 0; i < romanNumerals.Count; i++)
{
    while (remainingRomanNumeral.Length > 0 && remainingRomanNumeral[0] == romanNumerals.ElementAt(i).Key)
    {
        if (remainingRomanNumeral.Length > 1 && romanNumerals.ElementAt(i).Value < romanNumerals[remainingRomanNumeral[1]])
        {
            result -= romanNumerals.ElementAt(i).Value;
        }
        else
        {
            result += romanNumerals.ElementAt(i).Value;
        }

        remainingRomanNumeral.Remove(0, 1);
        i = 0;
    }
} 
            
return result;
```

Notice that I am removing the character after I have dealt with it. This is so we can carry on with the next characters without worrying about the previous. What may also look odd is that I am setting i to zero. This is so
we can iterate over the roman numerals dictionary from the beginning for the new character.

Now we have a solution that will work for stuff like IX, XI and so forth. However, there is a problem with this solution and it is the fact that there is no validation. The input can be "IIII" and the result would be 4. This is an incorrect roman numeral
so it should not be dealt with initially. In Part 2 I will add some validation to the input.
# Milan Suresh Lab Report 3

In this week, we will be looking at one of the bugs in week 4's lab and researching the `grep` command.

# Part 1

Let's look at the `reverseInPlace` method. It's function is to reverse the values of a given array.
```
static void reverseInPlace(int[] arr) {
    for(int i = 0; i < arr.length; i += 1) {
      arr[i] = arr[arr.length - i - 1];
    }
  }
```

Here's a failure-inducing test. We reverse an input array with multiple values, a procedure the method should do just fine:

```

@Test
  public void testReverseInPlaceWithValues(){
    int[] input1 = {2,5,6,7};
    ArrayExamples.reverseInPlace(input1);
    int[] reversed = {7,6,5,2};
    assertArrayEquals(reversed, input1);
  }
```
Here's a non-failure inducing test. We use just a single value in the input array:
```
public void testReverseInPlace() {
    int[] input1 = { 3 };
    ArrayExamples.reverseInPlace(input1);
    assertArrayEquals(new int[]{ 3 }, input1);
	}
```

We can see the symptom in the results of the `JUnit` tests. The non-failure-inducing test succeeds, while the failure-inducing test fails. 

<img width="773" alt="Screenshot 2024-02-13 at 10 45 36 PM" src="https://github.com/MilanSuresh2468/cse15llab3/assets/73302110/ea1b07ac-b259-489b-bc17-f2fb8f780694">

Let's fix this. 

Before:
```
static void reverseInPlace(int[] arr) {
    for(int i = 0; i < arr.length; i += 1) {
      arr[i] = arr[arr.length - i - 1];
    }
  }
```
After:
```
static void reverseInPlace(int[] arr) {
    int[]dummy = new int[arr.length];

    for (int i = 0; i < arr.length;i++){
      dummy[i]=arr[i];
    }
    for(int i = 0; i < arr.length; i += 1) {
      arr[i] = dummy[arr.length - i - 1];
    }
  }
```
This change fixes the issue since before, there was no `dummy` version of the original array we could get values from. As a result, whenever a value was changed,
the original value was lost forever, which presented issues. This still changes the original array as required but references a `dummy` array to get the values
to avoid this issue. 

# Part 2

Let's look at some ways we can use `grep`.

## Way 1

`grep -i` can be used to search with grep in a case-insensitive way, meaning capitalization does not matter. I found out 
about this through: https://www.geeksforgeeks.org/grep-command-in-unixlinux/

```
milansuresh@Milans-MacBook-Air-2 technical % grep -i "dNa" plos/*.txt >dna.txt
milansuresh@Milans-MacBook-Air-2 technical % wc dna.txt
     336    4900   41128 dna.txt
milansuresh@Milans-MacBook-Air-2 technical %  grep -i "dna" plos/*.txt >dna.txt
milansuresh@Milans-MacBook-Air-2 technical % wc dna.txt
     336    4900   41128 dna.txt
```
As we can see, regardless of whether we use the argument `"dNa"` or `"dna"`, we get the same result from `wc` because `grep` is being case insensitive.
This is useful in case the file uses varying capitalizations for DNA.

```

milansuresh@Milans-MacBook-Air-2 technical % grep -i "bAse" biomed/*.txt > base.txt
milansuresh@Milans-MacBook-Air-2 technical % wc base.txt
    5757   53522  514935 base.txt
milansuresh@Milans-MacBook-Air-2 technical % grep -i "basE" biomed/*.txt > base.txt
wc%                                                                                                          
milansuresh@Milans-MacBook-Air-2 technical % wc base.txt
    5757   53522  514935 base.txt
```
Whether we use `"bAse"` or `"basE"`, we get the same result since `-i` makes `grep` case insensitive. This is useful in case `"base"` is used at the start of sentences
and has different capitalizations as a result.

## Way 2
We use `grep -v` when we do not want to consider the keyword. I found out 
about this through: https://www.geeksforgeeks.org/grep-command-in-unixlinux/. Let's look at some examples

```
milansuresh@Milans-MacBook-Air-2 technical % grep -v "base" biomed/*.txt > base.txt
milansuresh@Milans-MacBook-Air-2 technical % wc base.txt
  485294 3878150 38696680 base.txt
```
As we can see, we get much larger counts before since we are considering cases where the keywords isn't used, not where the keyword is used. This
is useful if we want to see how much a prevelant word isn't used, like `"base"`.

```
milansuresh@Milans-MacBook-Air-2 technical % grep -v "crime" 911report/*.txt > crime.txt
milansuresh@Milans-MacBook-Air-2 technical % wc crime.txt
   25704  318832 2884862 crime.txt
```
By using `-v`, we get to consider the cases where `"crime"` is not used, which can help us evaluate how significant a word is in conjunction with the count
of how often it IS used.

## Way 3

`grep -c` allows us to count how many lines in the file match the pattern.
I found out about this through: https://www.geeksforgeeks.org/grep-command-in-unixlinux/. Let's look at 2 examples:

```
milansuresh@Milans-MacBook-Air-2 technical % grep -c "base" biomed/*.txt
biomed/1468-6708-3-1.txt:20
biomed/1468-6708-3-10.txt:10
biomed/1468-6708-3-3.txt:4
biomed/1468-6708-3-4.txt:11
...
```
In each line, the amount of times `"base"` appears is printed after the colon because we include `-c`. This is useful because it makes
it super easy to count the number of lines something appears in, making it easier to do analysis.

```
milansuresh@Milans-MacBook-Air-2 technical % grep -c "law" 911report/preface.txt
2
```
In `preface.txt`, `"law"` appears twice, so `-c` makes it so that we get `2` as output as desired. This is useful because we can check appearances of
words in individual files super quick.

## Way 4

`-h` returns the lines that contain the desired argument and does not display the filenames. Source: https://www.geeksforgeeks.org/grep-command-in-unixlinux/

```
milansuresh@Milans-MacBook-Air-2 technical % grep -h "law" 911report/chapter-10.txt
                connections; in the chaos after the attacks, it was very difficult to reach law
                were lawfully held on immigration charges. Records indicate that 531 were deported,
                sharing of such information between the intelligence and law enforcement
                sharing between the intelligence and law enforcement communities (discussed in
                Congress by large majorities and was signed into law on October 26.
```
Because we specify `"law"`, we get all the lines with the phrase in it returned to us. This is useful in case we are reading a big file and just
want to read the parts that pertain to a specific topic, like legality.

```
grep -h "Indonesia" plos/*.txt
        ecologically varied as, say, Brazil, South Africa, Australia, Indonesia, and the United
        imaging cannot detect many fires under the forest canopy. In Indonesia, wildfires that
        Brady and Indonesian university scientists grouped grasslands in the fine fuel category,
        Indonesia and Malaysia, to obtain and use the appropriate computing tools.
        evolutionary position of the 18,000-year-old “hobbit” recently unearthed on the Indonesian
        work for well-funded institutions. A group of researchers in Indonesia, for example,
        persistently agricultural economies with large populations (such as Nigeria and Indonesia)
```

We specify `"Indonesia"`, so the output is all the lines mentioning the country. This is useful if we are researching `"Indonesia"` specifically and 
just want to read the parts of the report about Indonesia.

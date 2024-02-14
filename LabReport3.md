# Milan Suresh Lab Report 3

In this week, we will be looking at one of the bugs in week 4's lab and researching facts about a command of my choice.

# Part 1

We will be analyzing this code

```
    public class StrangeMethod {
    //finds odd numbers and averages them
    public static double oddAverage(int[] arr) {
        double sum = 0;
        for(int i = 0; i < arr.length; i += 1) {
          if(arr[i]%2 == 1){
            sum+= (double) arr[i];
          }
        }
        return sum/ ( (double) arr.length);
      }
    
    }```



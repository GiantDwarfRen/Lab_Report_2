# Lab Report 2
## part one
Below are the code of `Server.java`:
```
# Server.java
import java.io.IOException;
import java.net.URI;

class Handler implements URLHandler {
    // The one bit of state on the server: a number that will be manipulated by
    // various requests.
    String content = "";

    public String handleRequest(URI url) {

        if (url.getPath().equals("/")) {
            return content;
        }
        else if (url.getPath().equals("/add-message")) {
            String[] parameters = url.getQuery().split("=");
            if (parameters[0].equals("s")) {
                content += '\n';
                content += parameters[1];
                return content;
            }
            return "404 Not Found!";
        }
        else {
            return "404 Not Found!";
        }
    }
}

public class StringServer {
    public static void main(String[] args) throws IOException {
        if(args.length == 0){
            System.out.println("Missing port number! Try any number between 1024 to 49151");
            return;
        }

        int port = Integer.parseInt(args[0]);

        Server.start(port, new Handler());
    }
}
```
Here is what I run on my terminal

![terminal.png](/images/terminal.png)

**initial state**

The first thing see in my webserver is a white page. 
Because I set root to just display the String we have so far.
Since we just initialize the server, the `content` should be empty String.

![root](/images/root.png)

**first command**

The first command is `http://localhost:4003/add-message?s=Vincent%27%20Lab%20Report%202`
And the website turns out to be:

![String-1](/images/String-1.png)

1. This calls `Handler.handleRequest(URI url)` method. It triggers the "else if" branch (line 14)
2. The argument passes to the method is `http://localhost:4003/add-message?s=Vincent%27%20Lab%20Report%202`, and `getPath()` would return `/add-message`, and `getQuery()` would return `s=Vincent%27%20Lab%20Report%202`.
The if statement judges it should go to "else if" branch (line 14), and split the query into `parameters[]` by "=".
`parameters[0]` would be "s", and `parameters[1]` would be "Vincent%27%20Lab%20Report%202".
3. Then, content would be appended with a new line character '\n', and then be appended with `parameters[1]`.
So far, content changes from "" to "\nVincent%27%20Lab%20Report%202".

**Second command**

The second command is `http://localhost:4003/add-message?s=How%20are%20you`
And the website turns out to be:

![String-2](/images/String-2.png)

1. This calls `Handler.handleRequest(URI url)` method. It triggers the "else if" branch (line 14)
2. The argument passes to the medthods is `http://localhost:4003/add-message?s=How%20are%20you`, and `getPath()` would return `/add-message`, and `getQuery()` would return `s=How%20are%20you`.
The if statement judeges it shoud go to "else if" branch (line 14), and split the query into `parameters[]` by "=".
`parameters[0]` would be "s", and `parameters[1]` would be "How%20are%20you".
3. Then, content would be appended with a new line character '\n', and then be appended with `parameters[1]`.
Till now, content changes to ""\nVincent%27%20Lab%20Report%202\nHow%20are%20you".

## part two

1. Failure test case

Below is the code from `ArrayExamples.java`
```
// Changes the input array to be in reversed order
    static void reverseInPlace(int[] arr) {
      for(int i = 0; i < arr.length; i += 1) {
        arr[i] = arr[arr.length - i - 1];
      }
    }
```
And Below is the failing test case code
```
@Test 
  public void MytestReverseInPlace() {
    int[] input1 = {1, 2, 3};
    ArrayExamples.reverseInPlace(input1);
    assertArrayEquals(new int[]{3, 2, 1}, input1);
  }
```

2. Pass test

Below is the code for a passing test (The one from the original lab 3 test code):
```
@Test 
    public void testReverseInPlace() {
        int[] input1 = { 3 };
        ArrayExamples.reverseInPlace(input1);
        assertArrayEquals(new int[]{ 3 }, input1);
    }
```

3. symptoms

Below is the screenshot of test result

![fail-test](/images/fail-test.png)

The symptom was that the value at index 2 was 3 but we expect 1. This means the value at index 2 was not reversed( should be the value at index 0).

Here is the image of test result:

![pass-test](/images/pass-test.png)

4. Bug fix

Original code:
```
// Changes the input array to be in reversed order
    static void reverseInPlace(int[] arr) {
      for(int i = 0; i < arr.length; i += 1) {
        arr[i] = arr[arr.length - i - 1];
      }
    }
```
Fixed code:
```
// Changes the input array to be in reversed order
  static void reverseInPlace(int[] arr) {
    for(int i = 0; i < arr.length / 2; i += 1) {
      int temp = arr[i];
      arr[i] = arr[arr.length - i - 1];
      arr[arr.length - i - 1] = temp;
    }
```

5. Analysis

*For the pass case:*

We expect the result to be `{3, 2, 1}`. However, the tests fails at index 2. We expect the value at index 2 to be 1, but it was 3.

*For the fail case:*

The test passed because there is only one element in the test array. The for loop would iterate just one time, casuing swapping values between `arr[0]` and `arr[1-0-1]` which is just `arr[0]` itself. That's why the test passed.

*Bug:*

There are 2 bugs in the original code:
- It overwrite values at lower half indices by the values at upper half indices, without swapping the values. So, here I declare a new variable “temp” to help swapping.
- Up till swapping to the half index number, all elements should be correctly swapped. But the for loop continue to traverse all index of the array. This cause values to be swapped back again! So, i set the for loop to stop at half. This works for both odd and even case. For odd case, the middle one does not necessarily have to be swapped. For even case, all values will be swapped correctly.

## Lab Report 2
# part one
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
![terminal.png][/images/terminal.png]
**initial state**
The first thing see in my webserver is a white page. 
Because I set root to just display the String we have so far.
Since we just initial the server, the `content` should be empty String.
![root][/images/root.png]
**first command**
The first command is `http://localhost:4003/add-message?s=Vincent%27%20Lab%20Report%202`
And the website turns out to be:
![String-1][/images/String-1.png]
1. This calls `Handler.handleRequest(URI url)` method. It triggers the "else if" branch (line 14)
2. The argument passes to the method is ``http://localhost:4003/add-message?s=Vincent%27%20Lab%20Report%202`, and `getPath()` would return `/add-message`, and `getQuery()` would return `s=Vincent%27%20Lab%20Report%202`.
The if statement judges it should go to "else if" branch (line 14), and split the query into `parameters[]` by "=".
`parameters[0]` would be "s", and `parameters[1]` would be "Vincent%27%20Lab%20Report%202".
3. Then, content would be appended with a new line character '\n', and then be appended with `parameters[1]`.
So far, content changes from "" to "\nVincent%27%20Lab%20Report%202".
**Second command**
The second command is `http://localhost:4003/add-message?s=How%20are%20you`
And the website turns out to be:
![String-2][/images/String-2.png]
1. This calls `Handler.handleRequest(URI url)` method. It triggers the "else if" branch (line 14)
2. The argument passes to the medthods is `http://localhost:4003/add-message?s=How%20are%20you`, and `getPath()` would return `/add-message`, and `getQuery()` would return `s=How%20are%20you`.
The if statement judeges it shoud go to "else if" branch (line 14), and split the query into `parameters[]` by "=".
`parameters[0]` would be "s", and `parameters[1]` would be "How%20are%20you".
3. Then, content would be appended with a new line character '\n', and then be appended with `parameters[1]`.
Till now, content changes to ""\nVincent%27%20Lab%20Report%202\nHow%20are%20you".

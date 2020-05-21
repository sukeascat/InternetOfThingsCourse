# 95-733 Internet of Things  Demonstration code

This is a "hello world" example using JSON and AJAX.

A Javascript JSON parsing libary is in IntelliJ under the Web Pages folder.
This file is named "json_sans_eval.js".

A very small Javascript file is also under the Web Pages folder and is shown next:
Its name is "GetJSON.js".

```

function getSomeJSON() {

     var xhr;

     if(window.XMLHttpRequest)
           xhr = new XMLHttpRequest();
     else
           xhr = ActiveXObject("Microsoft.XMLHTTP");

     xhr.onreadystatechange = function() {

             // handle callback by calling handleJSON
             if(xhr.readyState == 4) {

                 var result = xhr.responseText;

                 handleJSON(result);
             }
     }
     // Visit the servlet
     xhr.open("GET", "JSONHelloServlet",true);
     xhr.send(null);
}

// handle response
function handleJSON(someJSONFromServer) {

     // Use library to parse the string back from server
     var myJsonObj = jsonParse(someJSONFromServer);

     // Find the value of the Hello identifier
     var returnedValue = myJsonObj.Hello;

     // Place the returnedValue in the DOM.
     // The id coolLocation is used to find the location.
     var contents = document.getElementById("coolLocation");

     // Erase current value
     contents.innerHTML = "";
     // Add the value of the JSON name, value pair.
     contents.appendChild(document.createTextNode(returnedValue));
}
```


Shown next is the index.jsp page. A JSP (Java Server Page) page is compiled to a Java servlet
and runs on the server.

```
<%--
    Document   : index
    Created on : September 2019, 10:56:01 PM
    Author     : mm6
--%

<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">

        <!-- Have the browser fetch two javascript files. -->
        <script type="text/javascript" language="javascript" src="GetJSON.js"></script>
        <script type="text/javascript" language="javascript" src="json_sans_eval.js"></script>
        <title>Hello JSON</title>
    </head>
    <body>
        <h1>Hello JSON</h1>
        <!-- This button causes the servlet to be called
             and gets back a JSON object in a full page
             reload.
        -->
        <form action="JSONHelloServlet">
            <input type="submit" value="CoolSynchronouseSubmitButton" />
        </form>

        <button onclick="getSomeJSON()">Get JSON Asynch</button>

        <h1 id="coolLocation">JSON goes here.</h1>

    </body>
</html>
```

Shown next is the web.xml file. It describes the welcome page and a single servlet.

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <welcome-file-list>
        <welcome-file>index.jsp</welcome-file>
    </welcome-file-list>

    <servlet>
        <servlet-name>JSONHelloServlet</servlet-name>
        <servlet-class>JSONHelloServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>JSONHelloServlet</servlet-name>
        <url-pattern>/JSONHelloServlet</url-pattern>
    </servlet-mapping>
</web-app>

```


And finally, here is the servlet. Note that the servlet may be called
with a full page refresh or with an XHR request. That is, it may be called
when the browser first visits with an HTTP GET request or with an HTTP GET
requests generated by an XHR object from within Javascript.

```
import java.io.IOException;
import java.io.PrintWriter;
import java.util.Iterator;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 *
 * @author Mike McCarthy
 * This is a simple servlet that returns a JSON string to the browser.
 *
 */

public class JSONHelloServlet extends HttpServlet {


    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {

        System.out.println("Servlet hit");
        String coolJSONString = "";

        response.setContentType("application/json");

        StringBuffer json = new StringBuffer();

        // A JSON object {"Hello":"World JSON"}

        json.append("{\"Hello\":\"Hello JSON\"}");

        coolJSONString = json.toString();

        response.getWriter().write(coolJSONString);

    }
}
```
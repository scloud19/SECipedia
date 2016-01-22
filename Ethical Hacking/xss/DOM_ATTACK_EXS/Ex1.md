Suppose the following code is used to create a form to let the user choose his/her preferred language. A default language is also provided in the query string, as the parameter “default”.
…

Select your language:

<select><script>

document.write("<OPTION value=1>"+document.location.href.substring(document.location.href.indexOf("default=")+8)+"</OPTION>");

document.write("<OPTION value=2>English</OPTION>");

</script></select>
…



The page is invoked with a URL such as:
http://www.some.site/page.html?default=French

A DOM Based XSS attack against this page can be accomplished by sending the following URL to a victim:

http://www.some.site/page.html?default=<script>alert(document.cookie)</script>

When the victim clicks on this link, the browser sends a request for:

/page.html?default=<script>alert(document.cookie)</script>

to www.some.site. The server responds with the page containing the above Javascript code. The browser creates a DOM object for the page, in which the document.location object contains the string:
http://www.some.site/page.html?default=<script>alert(document.cookie)</script>

The original Javascript code in the page does not expect the default parameter to contain HTML markup, and as such it simply echoes it into the page (DOM) at runtime. The browser then renders the resulting page and executes the attacker’s script:

alert(document.cookie)

Note that the HTTP response sent from the server does not contain the attacker’s payload. This payload manifests itself at the client-side script at runtime, when a flawed script accesses the DOM variable document.location and assumes it is not malicious.

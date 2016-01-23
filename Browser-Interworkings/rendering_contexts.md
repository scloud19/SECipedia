When a browser is rendering HTML and any other associated content like CSS, javascript, etc. it identifies various rendering contexts for the different kinds of input and follows different rules for each context. A rendering context is associated with the parsing of HTML tags and their attributes. 


The HTML parser of the rendering context dictates how data is presented and laid out on the page and can be further broken down into the standard contexts of HTML, HTML attribute, URL, and CSS. The JavaScript or VBScript parser of an execution context is associated with the parsing and execution of script code. Each parser has distinct and separate semantics in the way they can possibly execute script code which make creating consistent rules for mitigating vulnerabilities in various contexts difficult. 

The complication is compounded by the differing meanings and treatment of encoded values within each subcontext (HTML, HTML attribute, URL, and CSS) within the execution context.


For the purposes of this article, we refer to the HTML, HTML attribute, URL, and CSS Cheatsheet contexts as subcontexts because each of these contexts can be reached and set within a JavaScript execution context. In JavaScript code, the main context is JavaScript but with the right tags and context closing characters, an attacker can try to attack the other 4 contexts using equivalent JavaScript DOM methods.

The following is an example vulnerability which occurs in the JavaScript context and HTML subcontext:


Rendering of CSS, Javascript, HTML, etc.
  The browser has a distinct rendering process for each type of content that it renders (CSS, JS, etc.)

  Each rendering context is parsed with specific rules (that are specific to that content type)
    Additionally, each parser has distinct and separate semantics of the way that it can possibly execute script code.
      Also, from an exploit perspective, the issue is compounded by the different treatment of encoded values within each SUBcontext (ex: HTML, HTML attribute, URL, CSS)
  
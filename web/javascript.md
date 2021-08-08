# Javascript

## Javascript

[Something on sandboxes](https://d0nut.medium.com/why-building-a-sandbox-in-pure-javascript-is-a-fools-errand-d425b77b2899)

### Character restrictions

[Only \(\)+\[\]!](http://www.jsfuck.com/)    
[Only +\[\]{}!$\`](https://portswigger.net/research/executing-non-alphanumeric-javascript-without-parenthesis)  
[No parentheses](https://portswigger.net/research/javascript-without-parentheses-using-dommatrix)  
[Cool encoder](https://xssor.io/)

If your injected code is HTML-escaped, you can use backticks \` to get strings through. Just be aware that these strings are templates, and will be interpolated.

### Unicode shenanigans

[Of course](https://portswigger.net/research/escaping-javascript-sandboxes-with-parsing-issues)

### Leaks

A function's `.toString()` renders its full source code, _including comments_!

```javascript
function a() {
    /* Top sneaky: flag{1234} */
}
console.log(a.toString());
```

### Exfiltration

#### Change the DOM inside an iframe

[Technique](https://research.securitum.com/marginwidth-marginheight-the-unexpected-cross-origin-communication-channel/)

The host page isn't supposed to communicate with other domains. If you spawn an `iframe` and change some DOM in the host, it will be inherited in the guest. Oops.


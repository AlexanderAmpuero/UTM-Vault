##### Intro to HTTP
HTTP allows your browser to communicate to the website, primarily used for interactions. 
HTTP stands for hypertext transfer protocol
Request-Response protocol between website and servers

and HTTP request consists of a method, path, version, and the headers. 
```html
GET / HTTP/1.1 /* method, path, version
Host: dveloper.mozilla.org /* headers
Accept-Language: en /* header
```

GET, POST, PUT, and DELETE are most common.
They retrieve, send, update, and remove respectively.

###### VITAL HTTP INFORMATION

HTTP can be used to transfer documents, images, and files

- Informational Responses, Successful Responses, Redirection Messages, Client Error Responses, Client Error Responses
- Each of the above categories contain 100 specific errors each, total of 500
-  HTTP comes with incryption
- before HTTP content is sent, in encrypts the website which can only be decrypted by the receiving user. 


#### HTML, CSS, and Javascript
Imagine you are building a website for a clock, if you had only used HTML to program this clock, then the content would be shown without any style, positioning, or quality.

To apply styling to the website, you give it to CSS who can change font, size, location, and general prettiness

Javascript ensures that the clock actually functions, such as actually increasing in time. This data is requested by the HTML and CSS.

Imagine you are making a website which contains a video, and a single button below it. The HTML code tell us that the video and button exist, where the CSS creates styling for the video and button which make it readable and usable. Lastly, the Javascript allows the video to be played and continuously checks if the video is currently playing, if not, updating the button to say that it is not playing. 

A typical website will contain all three of these languages.
- HTML by definition is hyperlinks

###### Web Application
A website is informative whereas a web application is more interactive, ex. Wikipedia v. UberEats
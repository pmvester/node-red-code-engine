# Node-RED on IBM Cloud Code Engine
## Setting up DNS to shield application and provide valid certificates.

![i010.png](/images/i010.png)
![i011.png](/images/i011.png)
![i012.png](/images/i012.png)
![i013.png](/images/i013.png)
![i014.png](/images/i014.png)

```
addEventListener('fetch', (event) => {
    const mutable_request = new Request(event.request);
    event.respondWith(directRequestToCodeEngineApp(mutable_request));
});
 
async function directRequestToCodeEngineApp(request) {
    // FQDN of the Code Engine application that should receive the traffic
    const targetHost = 'node-red.11ot3bdcttm5.eu-de.codeengine.appdomain.cloud';
     
    try {
      const url = new URL(request.url);
 
      // let the code engine know what origin the request came from
      request.headers.set('X-Forwarded-Host', url.hostname);
      request.headers.set('host', targetHost);
 
      // override the hostname
      url.hostname = targetHost;
 
      // enforce HTTPS towards Code Engine
      url.protocol = "https:";
     
      // pass the request to the application running on Code Engine
      return await fetch(url.toString(), request);
    } catch (error) {
      // in case of an error, play the regular request
      console.log(`Failed to pass request to '${targetHost}'`, error);
      return await fetch(request);
    }
}```

![i015.png](/images/i015.png)
![i015.png](/images/i015.png)
![i015.png](/images/i015.png)

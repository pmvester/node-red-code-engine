# Node-RED on IBM Cloud Code Engine
## Setting up DNS to shield application and provide valid certificates.

Start by copying the application URL and make a note of it, it is needed later.

![i010.png](/images/i010.png)

In IBM Cloud Internet Services navigate to the "Reliability" section and click on the "DNS" tab.

![i011.png](/images/i011.png)

Scroll down and click the "Add" button.

![i012.png](/images/i012.png)

Add a CNAME record with the name of your service and fill in the application URL that you copied in a previous step. (Strictly speaking, it is sufficient to fill in any valid FQDN as we will override this value in a subsequent step.)

![i013.png](/images/i013.png)

Navigate to the "Edge functions" section and select the "Actions" tab and click the "Create" button.

![i014.png](/images/i014.png)

Copy this code snippet as we will need it in the next step.

```
addEventListener('fetch', (event) => {
    const mutable_request = new Request(event.request);
    event.respondWith(directRequestToCodeEngineApp(mutable_request));
});
 
async function directRequestToCodeEngineApp(request) {
    // FQDN of the Code Engine application that should receive the traffic
    const targetHost = '<<application URL>>';
     
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
}
```

Replace the example code with the code snippet from the previous step. In the pasted code, replace `<<application URL>>` with the application URL that you took a note of in the first step.

Click on "Save".

![i015.png](/images/i015.png)
![i016.png](/images/i016.png)
![i017.png](/images/i017.png)

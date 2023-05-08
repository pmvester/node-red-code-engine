# Setting up Cloudflare

Find and click on your domain.

![i021.png](/images/i021.png)

Click on "Workers Routes".

![i022.png](/images/i022.png)

Click on "Manage Workers".

![i023.png](/images/i023.png)

Click on "Create a Service".

![i024.png](/images/i024.png)

Fill in the "Service name" and click "Create Service".

![i025.png](/images/i025.png)

Make a note of the Code Engine public domain name.

![i027.png](/images/i027.png)

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

Click on "Quick edit".

![i026.png](/images/i026.png)

Paste in the code and replace the `<<application FQDN>>` placeholder with the Code Engine public domain name that you took a note of in the previous step. Click "Save and deploy".

![i028.png](/images/i028.png)

Click on "Save and deploy" again.

![i029.png](/images/i029.png)

Scroll down and click on "Add Custom Domain".

![i030.png](/images/i030.png)

Fill in the "Domain" and click "Add Custom Domain".

![i031.png](/images/i031.png)

You will see that the certificate for the domain is "Initializing".

![i032.png](/images/i032.png)

Wait until the certificate is in its "Active" state.

![i033.png](/images/i033.png)

Your new route for the Code Engine is now active! Congratulations!

![i034.png](/images/i034.png)

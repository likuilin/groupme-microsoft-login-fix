# Groupme Microsoft login fix

## Update: Groupme fixed it! Woo~

This is a fix for the Groupme web client's Microsoft OAUTH login bug. 20-second video: https://youtube.com/watch?v=82XCpxKEhls

Currently, if you use https://web.groupme.com/, trying to log in using a Microsoft account leaves you on a white screen after entering your Microsoft account details. 

On that white screen, open the browser JS console, and run this javascript: 

```javascript
document.cookie = "token=" + (await (await fetch("https://api.groupme.com/v3/registrations/microsoft", {
    "headers": {
        "accept": "application/json, text/plain, */*",
        "content-type": "application/json;charset=UTF-8",
    },
    "body": JSON.stringify({
        "registration": {
            "microsoft_refresh_token": "skip",
            "microsoft_access_token": window.location.hash.split("access_token=").pop().split("&").shift(),
            "device_id": Math.random().toString().substr(2)
        }
    }),
    "method": "POST",
    "mode": "cors",
    "credentials": "omit"
})).json()).response.access_token.access_token;
window.location.reload();
```

Then, you should be logged in. 

Note: This will not work if you don't already have an account that you log into with Microsoft, because the registration flow is slightly different. So, this will only work for users who created their account before this bug using a Microsoft login, or users who created their account on the app using a Microsoft login. 

My code is licensed CC BY-NC 4.0. If anyone from Microsoft is reading this, I made this only to help people while you fix this bug; if you want it taken down, just say the word. Hint: check the Angular template, `msaAuthenticator` is never loaded, also the call to `/registrations/microsoft_sso` seems to not work properly.

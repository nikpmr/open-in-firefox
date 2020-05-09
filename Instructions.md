# Open In Firefox (for Vivaldi)

### Installing the extension

1. Go to https://chrome-extension-downloader.com/ and download [this extension](https://chrome.google.com/webstore/detail/open-in-firefox/lmeddoobegbaiopohmpmmobpnpjifpii/related)

2. Extract files with 7-zip, open common.js and paste the following at the bottom of the file:

   ```javascript
   // Open all new tabs in Firefox
   var openedTabs = [];
   var windowCreated = false;
   chrome.tabs.onCreated.addListener(function(info) {
     var url = info.pendingUrl || info.url;
     if(
       url.indexOf('chrome://') === -1 && 
       url.indexOf('chrome-extension://') === -1 && 
       !windowCreated
     ){
       openedTabs.push(info.id);
     }
     // console.log(info);
   });
   chrome.tabs.onUpdated.addListener(function(tabID, tab, info) {
     var url = info.pendingUrl || info.url;
     chrome.tabs.query({
       active: true,
       currentWindow: true
     }, tabs => {
       if(openedTabs.includes(tabID) && !windowCreated) {
         open([url],tabID);
         openedTabs = openedTabs.filter(e => e !== tabID);
         chrome.tabs.remove(tabID);
       }
       // console.log(tabID, info);
     });
   });
   chrome.windows.onCreated.addListener(function(){
     windowCreated = true;
     setTimeout(function(){
       windowCreated = false;
     },2000);
   })
   ```

3. In Vivaldi, go to chrome://extensions and enable developer mode, then click *Pack extension*. Create a CRX file and drag it into Vivaldi. The extension should side-load.

4. Copy the extension ID, then close Vivaldi



### Installing the client

1. Download the latest Windows release from [this link](https://github.com/andy-portmen/native-client/releases) and run *install.bat*
2. Go to *%LOCALAPPDATA%\com.add0n.node* and open *manifest-chrome.json*
3. Add the extension ID to the *allowed_origins* array
4. Reopen Vivaldi; new windows should open in Firefox now.

